<template>
	<div class="translation-container">
        <LanguagePanel
            :source="source"
            :result="result"
            @sourceClick="source.isActive = $event"
            @resultClick="result.isActive = $event"
            @swapClick="swapLangs"
            @deleteClick="deleteText"
        />

        <div class="text-box" ref="textBox">
            <div data-id="source-language" class="source box">
                <div class="language-box" :class="{active: source.isActive}">
                    <button
                        class="close-language-list fas fa-times"
                        v-show="isMobile"
                        @click="source.isActive = false"
                    ></button>

                    <LanguageList
                        :langCode="source.code"
                        :langs="langs"
                        @langChanged="
                            source.code = $event;
                            source.lang = langs[$event];
                            source.isActive = false;
                            translateText()
                        "
                    />
                </div>
    			<textarea
    				id="translation-source"
                    class="text-area"
    				placeholder="Type text"
    				@keydown.enter.prevent
                    @keyup="delayedTranslate"
                    v-model="source.text"
                ></textarea>
    		</div>

    		<div data-id="result-language" class="result box">
                <div class="language-box" :class="{active: result.isActive}">
                    <button
                        class="close-language-list fas fa-times"
                        v-show="isMobile"
                        @click="result.isActive = false"
                    ></button>

                    <LanguageList
                        :langCode="result.code"
                        :langs="langs"
                        @langChanged="
                            result.code = $event;
                            result.lang = langs[$event];
                            result.isActive = false;
                            translateText()
                        "
                    />
                </div>
                <div id="translation-result" class="text-area" v-show="isTranslated">{{result.text}}</div>
    		</div>
        </div>
	</div>
</template>

<script>
import LanguageList from "./LanguageList.vue";
import LanguagePanel from "./LanguagePanel.vue";

export default {
	name: "TranslationForm",

    props: ["isMobile"],

    components: {
        LanguageList,
        LanguagePanel
    },

    data() {
		return {
            apiKey: "trnsl.1.1.20190516T152855Z.374d3c99e14a4ecb.beeccad020a9581b4f5065efa06293e2ef18e9c6",
            typeTimer: null,
			langs: "",
            source: {
                code: "en",
                lang: "English",
                text: "",
                isActive: false
            },
            result: {
                code: "ru",
                lang: "Russian",
                text: "",
                isActive: false,
                temp: {
                    code: "",
                    lang: "",
                    text: ""
                }
            }
		}
	},

    methods: {
        delayedTranslate() {
            clearTimeout(this.typeTimer);

            this.typeTimer = setTimeout(() => {
                this.translateText();
            }, 500);
        },

		translateText() {
            if (this.source.text.trim().length == 0) {
                return this.result.text = "";
            }

            fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=${this.apiKey}
                &text=${this.source.text}&lang=${this.source.code}-${this.result.code}`)
            .then((response) => response.json())
            .then((data) => this.result.text = data.text[0])
            .catch((err) => console.error(err));
		},

        deleteText() {
            this.source.text = "";
            this.result.text = "";
        },

        getLangs() {
            fetch("https://translate.yandex.net/api/v1.5/tr.json/getLangs?ui=en&key="+this.apiKey)
            .then((response) => response.json())
            .then((data) => {
                this.langs = Object.fromEntries(Object.entries(data.langs).sort((a, b) => {
                    return (a[1] > b[1]) ? 1 : (a[1] < b[1]) ? -1 : 0;
                }));
            })
            .catch((err) => console.error(err));
        },

        swapLangs() {
            this.result.temp = this.source;
            this.source = this.result;
            this.result = this.result.temp;
            this.source.isActive = false;
            this.result.isActive = false;
            this.translateText();
        },


	},

    created() {
        this.getLangs();
    },

    computed: {
        isTranslated() {
            if (this.isMobile) {
                return (this.result.text) ? true : false;
            }

            return true;
        }
    }
}
</script>

<style lang="scss" scoped>
@import "../styles/base/_variables.scss";
@import "../styles/base/_mixins.scss";

.translation-container {
    position: relative;
}

.text-box {
    overflow: hidden;
    @include grid;
    @include breakpoint($sm) {
        display: block;
    }
}

.box {
    overflow: hidden;
}

.language-box {
    position: absolute;
    top: 40px;
    left: 0;
    display: none;
    width: calc(50% - 25px);
    background: $color-white;
    border: 2px solid $color-2;
    box-shadow: 0 2px 8px -2px rgba(0,0,0,0.3);

    &#result-language {
        left: auto;
        right: 0;
    }

    &.active {
        display: block;
    }

    .box.result & {
        left: auto;
        right: 0;
    }

    @include breakpoint($sm) {
        position: fixed;
        top: 0;
        overflow-y: auto;
        width: 100%;
        height: 100%;
        padding: 30px 0;
        border: none;
        box-shadow: none;
    }
}

.text-area {
	width: 100%;
	min-height: 150px;
	padding: 10px;
    font-size: $fontSize-big;
	color: $color-1;
    border: $borderWidth $color-2 solid;
    border-bottom-left-radius: $borderRadius;
    border-bottom-right-radius: $borderRadius;
    resize: none;

    @include breakpoint($sm) {
        &#translation-source {
            padding-right: 30px;
        }

        &#translation-result {
            min-height: auto;
            word-break: break-word;
            margin-top: 15px;
            background: lighten($color-2, 15%);
            border-radius: $borderRadius;
        }
    }
}

::placeholder {
	color: lighten($color-1, 40%);
}

.close-language-list {
    position: absolute;
    top: 0;
    right: 0;
    width: 30px;
    height: 30px;
    font-size: 20px;
    color: $color-1;
}
</style>