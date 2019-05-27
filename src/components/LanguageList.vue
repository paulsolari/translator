<template>
    <ul class="language-wrapper">
        <li
            v-for="(lang, code) in langs"
            :key="code"
            :data-code="code"
            :class="{active: code == langCode}"
            @click="changelang"
        >{{lang}}</li>
    </ul>
</template>

<script>
export default {
    name: "LanguageList",

    props: ["langCode", "langs"],

    methods: {
        changelang(event) {
            this.$emit("langChanged", event.target.getAttribute("data-code"));
        }
    }
}
</script>

<style lang="scss" scoped>
@import "../styles/base/_variables.scss";
@import "../styles/base/_mixins.scss";

.language-wrapper {
    display: flex;
    flex-wrap: wrap;
    font-size: $fontSize-small;

    li {
        padding: 10px;
        cursor: pointer;
        transition: background-color $transitionDuration;

        &.active {
            background-color: $color-3;
        }

        @include hover {
            background-color: $color-3;
        }
    }
}
</style>