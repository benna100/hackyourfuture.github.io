<template>
  <header class="header">
    <div class="container max-width">
      <div :class="{pressed: menuOpen}" class="icon menu-button" @click="toggleMenu">
        <span class="icon-menu"/>
        <span class="icon-cross"/>
      </div>
      <HeaderNav/>
      <div class="mobile-logo">
        <img src="/logos/logo-2.svg" alt>
      </div>
    </div>
  </header>
</template>

<script>
import eventBus from "~/utils/event-bus";
import HeaderNav from "./headerNav";
import HeaderNavMobile from "./headerNavMobile";

export default {
    components: {
        HeaderNav,
        HeaderNavMobile
    },
    data() {
        return {
            menuOpen: false
        };
    },
    watch: {
        "$route.name": function() {
            this.menuOpen = false;
        }
    },
    methods: {
        toggleMenu() {
            this.menuOpen = !this.menuOpen;
            eventBus.$emit("toggle-mobile-nav", this.menuOpen);
        }
    }
};
</script>

<style lang="scss">
.header {
    position: fixed;
    width: 100%;
    background: white;
    z-index: 10;
    top: 0;
    left: 0;
    @include breakpoint("mobile_landscape") {
        height: 60px;
    }
    .mobile-logo {
        display: none;
        position: absolute;
        width: 60px;
        height: 60px;

        top: 20px;
        @include breakpoint("mobile_landscape") {
            right: 60px;
            display: block;
        }
        @include breakpoint("mobile_portrait") {
            right: 40px;
        }
    }

    .headerNav {
        margin: $base-vertical-rithm * 2;
        text-align: center;
        @include breakpoint("mobile_landscape") {
            display: none;
        }
    }

    .menu-button {
        top: 20px;
        position: absolute;
        left: 20px;
        width: 22px;
        height: 22px;
        overflow: hidden;
        display: none;
        @include breakpoint("mobile_landscape") {
            display: block;
        }
        &.pressed {
            .icon-menu {
                transform: translateX(50px);
            }
            .icon-cross {
                transform: translateX(0px);
            }
        }
        .icon-menu,
        .icon-cross {
            position: absolute;
            transition: transform 0.25s ease-out;
            background-size: cover;
            background-repeat: no-repeat;
            top: 0px;
            width: 100%;
            height: 100%;
        }
        .icon-menu {
            background-image: url("/icons/002-menu-button.svg");
        }
        .icon-cross {
            background-image: url("/icons/001-cancel.svg");
            transform: translateX(-50px);
        }
    }
}
</style>
