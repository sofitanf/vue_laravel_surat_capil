<template>
	<div :class="containerClass" @click="onWrapperClick">
		<AppTopBar @menu-toggle="onMenuToggle" />

		<transition name="layout-sidebar">
			<div
				:class="sidebarClass"
				@click="onSidebarClick"
				v-show="isSidebarVisible()"
			>
				<AppProfile />
				<AppMenu :menu="menu" @menuitem-click="onMenuItemClick" />
			</div>
		</transition>

		<div class="layout-main">
			<router-view />
		</div>
	</div>
</template>

<script>
import AppTopBar from "../components/layout/AppTopbar.vue";
import AppProfile from "../components/layout/AppProfile.vue";
import AppMenu from "../components/layout/AppMenu.vue";
import AppFooter from "../components/layout/AppFooter.vue";

export default {
	data() {
		return {
			layoutMode: "static",
			layoutColorMode: "dark",
			staticMenuInactive: false,
			overlayMenuActive: false,
			mobileMenuActive: false,
		};
	},
	watch: {
		$route() {
			this.menuActive = false;
			this.$toast.removeAllGroups();
		},
	},
	methods: {
		onWrapperClick() {
			if (!this.menuClick) {
				this.overlayMenuActive = false;
				this.mobileMenuActive = false;
			}

			this.menuClick = false;
		},
		onMenuToggle() {
			this.menuClick = true;

			if (this.isDesktop()) {
				if (this.layoutMode === "overlay") {
					if (this.mobileMenuActive === true) {
						this.overlayMenuActive = true;
					}

					this.overlayMenuActive = !this.overlayMenuActive;
					this.mobileMenuActive = false;
				} else if (this.layoutMode === "static") {
					this.staticMenuInactive = !this.staticMenuInactive;
				}
			} else {
				this.mobileMenuActive = !this.mobileMenuActive;
			}

			event.preventDefault();
		},
		onSidebarClick() {
			this.menuClick = true;
		},
		onMenuItemClick(event) {
			if (event.item && !event.item.items) {
				this.overlayMenuActive = false;
				this.mobileMenuActive = false;
			}
		},
		onLayoutChange(layoutMode) {
			this.layoutMode = layoutMode;
		},
		onLayoutColorChange(layoutColorMode) {
			this.layoutColorMode = layoutColorMode;
		},
		addClass(element, className) {
			if (element.classList) element.classList.add(className);
			else element.className += " " + className;
		},
		removeClass(element, className) {
			if (element.classList) element.classList.remove(className);
			else
				element.className = element.className.replace(
					new RegExp(
						"(^|\\b)" + className.split(" ").join("|") + "(\\b|$)",
						"gi"
					),
					" "
				);
		},
		isDesktop() {
			return window.innerWidth > 1024;
		},
		isSidebarVisible() {
			if (this.isDesktop()) {
				if (this.layoutMode === "static") return !this.staticMenuInactive;
				else if (this.layoutMode === "overlay") return this.overlayMenuActive;
				else return true;
			} else {
				return true;
			}
		},
	},
	computed: {
		menu() {
			let item = [];

			item = [
				{
					label: "Dashboard",
					icon: "pi pi-fw pi-home",
					to: "/dashboard",
				},
				{
					label: "Surat Masuk",
					icon: "pi pi-fw pi-envelope",
					to: "/surat-masuk",
				},
				{
					label: "Surat Keluar",
					icon: "pi pi-fw pi-envelope",
					to: "/surat-keluar",
				},
				{
					label: "Nomor Surat",
					icon: "pi pi-fw pi-ticket",
					to: "/nomor-surat",
				},
			];

			return item;
		},
		containerClass() {
			return [
				"layout-wrapper",
				{
					"layout-overlay": this.layoutMode === "overlay",
					"layout-static": this.layoutMode === "static",
					"layout-static-sidebar-inactive":
						this.staticMenuInactive && this.layoutMode === "static",
					"layout-overlay-sidebar-active":
						this.overlayMenuActive && this.layoutMode === "overlay",
					"layout-mobile-sidebar-active": this.mobileMenuActive,
					"p-input-filled": this.$appState.inputStyle === "filled",
					"p-ripple-disabled": this.$primevue.config.ripple === false,
				},
			];
		},
		sidebarClass() {
			return [
				"layout-sidebar",
				{
					"layout-sidebar-dark": this.layoutColorMode === "dark",
					"layout-sidebar-light": this.layoutColorMode === "light",
				},
			];
		},
		logo() {
			return this.layoutColorMode === "dark"
				? "assets/layout/images/logo-white.svg"
				: "assets/layout/images/logo.svg";
		},
	},
	beforeUpdate() {
		if (this.mobileMenuActive)
			this.addClass(document.body, "body-overflow-hidden");
		else this.removeClass(document.body, "body-overflow-hidden");
	},
	components: {
		AppTopBar: AppTopBar,
		AppProfile: AppProfile,
		AppMenu: AppMenu,
		AppFooter: AppFooter,
	},
	// created() {
	// 	if (!this.$store.getters.isLoggedIn) {
	// 		this.$router.push("/login");
	// 	}
	// },
};
</script>
<style>
.fc-event-main {
	cursor: pointer;
}
</style>