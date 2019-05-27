<template>
	<div id="app" :class="{touch: isTouch, mobile: isMobile}">
		<div class="wrapper">
            <h1>Translator</h1>
			<TranslationForm :isMobile="isMobile"/>
		</div>
	</div>
</template>

<script>
import TranslationForm from "./components/TranslationForm.vue";

export default {
	name: "app",

    components: {
        TranslationForm
    },

    data() {
        return {
            isTouch: 'ontouchstart' in document.documentElement,
            isMobile: false
        }
    },

    methods: {
        onResize() {
            this.isMobile = (window.innerWidth < 768) ? true : false;
        }
    },

    created() {
        this.onResize();
    },

    mounted() {
        window.addEventListener('resize', this.onResize);
    }
}
</script>

<style lang="scss">
@import "./styles/main.scss";

body {
    font: 400 #{$fontSize-medium}/1 $font-main;
    color: $color-black;
}

.wrapper {
	max-width: 1240px;
	margin: 0 auto;
	padding: 30px 15px;

    @include breakpoint($sm) {
        padding: 15px;
    }
}

h1 {
    padding-bottom: 40px;
    text-align: center;
    font: 700 $fontSize-h1 $font-secondary;
    color: $color-3;
    background: linear-gradient(to bottom, $color-3 15%, $color-4 85%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;

    @include breakpoint($sm) {
        padding-bottom: 25px;
        font-size: calc(#{$fontSize-h1} - 20px);
    }
}
</style>