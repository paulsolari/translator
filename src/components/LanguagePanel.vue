<template>
    <div class="panel">
        <div class="panel-box">
            <button
                id="source-language"
                class="language-button"
                ref="sourceLangBtn"
                :data-code="source.code"
                :class="{active: source.isActive}"
                @click="toggleSourceActive"
            >{{source.lang}}</button>
        </div>

        <button class="swap-languages-button fas fa-arrows-alt-h" @click="swapLangs"></button>
        <button class="delete-text-button fas fa-times" @click="$emit('deleteClick')" v-show="source.text"></button>

        <div class="panel-box">
            <button
                id="result-language"
                class="language-button"
                ref="resultLangBtn"
                :data-code="result.code"
                :class="{active: result.isActive}"
                @click="toggleResultActive"
            >{{result.lang}}</button>
        </div>
    </div>
</template>

<script>
export default {
    name: "LanguagePanel",

    props: ["source", "result"],

    data() {
        return {
            sourceActive: false,
            resultActive: false
        }
    },

    methods: {
        toggleSourceActive() {
            this.sourceActive = !this.source.isActive;
            this.resultActive = false;
            this.$emit("sourceClick", this.sourceActive);
            this.$emit("resultClick", this.resultActive);
        },

        toggleResultActive() {
            this.resultActive = !this.result.isActive;
            this.sourceActive = false;
            this.$emit("resultClick", this.resultActive);
            this.$emit("sourceClick", this.sourceActive);
        },

        swapLangs() {
            this.$emit("swapClick");
        }
    }
}
</script>

<style lang="scss" scoped>
@import "../styles/base/_variables.scss";
@import "../styles/base/_mixins.scss";

.panel {
    @include grid;
}

.panel-box {
    display: flex;
    border: 2px solid $color-2;
    border-bottom: none;
    border-top-left-radius: $borderRadius;
    border-top-right-radius: $borderRadius;

    &:first-child {
        justify-content: flex-end;
    }

    @include breakpoint($sm) {
        display: block;
    }
}

.language-button {
    padding: 10px;
    font-size: $fontSize-medium;
    letter-spacing: 0.05em;
    font-weight: 700;
    color: $color-1;
    transition: color $transitionDuration;

    &.active {
        color: $color-3;
    }

    @include hover {
        color: $color-3;
    }

    @include breakpoint($sm) {
        width: 100%;
        font-size: $fontSize-small;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }
}

.swap-languages-button,
.delete-text-button {
    position: absolute;
    top: 0;
    left: 50%;
    margin-left: -25px;
    width: 50px;
    height: 40px;
    color: $color-1;
    transition: color $transitionDuration;

    @include hover {
        color: $color-3;
    }

    &::before {
        font-size: 33px;
        color: currentColor;
    }

    @include breakpoint($sm) {
        margin-left: -15px;
        width: 30px;

        &::before {
            font-size: 20px;
        }
    }
}

.delete-text-button {
    top: 40px;

    @include breakpoint($sm) {
        height: 30px;
        margin: 0;
        right: 0;
        left: auto;
    }
}
</style>