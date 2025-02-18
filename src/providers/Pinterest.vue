<template>
  <a
    class="button-social"
    :class="buttonSocialDesignObject"
    :page-url="page_url"
    :page-description="page_description"
    :button-design="button_design"
    :title-social="title_social"
    :has-icon="has_icon"
    :has-square-edges="has_square_edges"
    :has-counter="has_counter"
    @click.prevent="showShareWindow"
  >
    <i class="icon-pinterest" v-if="this.$props.has_icon"></i>
    <span class="title-social" v-if="this.$props.title_social">
      {{ title_social }}
    </span>
    <span
      class="counter-pinterest"
      v-if="this.$props.has_counter"
    >
      {{ counter_pinterest }}
    </span>
  </a>
</template>

<script>
import { clickEvent } from "../helpers/events";
import { documentHref } from "../helpers/href";
import { metaDescription } from "../helpers/description";
import { linkAppleTouchIcon } from "../helpers/icon";
import { getCallbackName } from "../helpers/callback_name";
import { sliceThousandInt } from "../helpers/count_number";
import { openPopUpWindow } from "../helpers/popup_window";

export default {
  name: "VueGoodsharePinterest",
  props: {
    page_url: {
      type: String,
      default: documentHref
    },
    page_description: {
      type: String,
      default: metaDescription
    },
    page_image: {
      type: String,
      default: linkAppleTouchIcon
    },
    button_design: {
      type: String,
      default: () => "flat"
    },
    title_social: String,
    has_icon: Boolean,
    has_square_edges: Boolean,
    has_counter: Boolean
  },
  data() {
    return {
      buttonSocialDesignObject: {
        "button-social__square_edges": this.$props.has_square_edges,
        pinterest__design__flat: this.$props.button_design === "flat",
        pinterest__design__gradient: this.$props.button_design === "gradient",
        pinterest__design__outline: this.$props.button_design === "outline"
      },
      counter_pinterest: 0
    };
  },
  methods: {
    /**
     * Show share window.
     *
     * @return {object} a pop-up window
     */
    showShareWindow: function() {
      // Variables
      const width = 640;
      const height = 480;
      const share_url = `https://www.pinterest.com/pin/create/button/?canonicalUrl=${encodeURIComponent(
        this.$props.page_url
      )}&description=${encodeURIComponent(
        this.$props.page_description
      )}&media=${encodeURIComponent(this.$props.page_image)}`;

      // onClick event
      clickEvent(this, "pinterest");

      return openPopUpWindow(share_url, width, height);
    },

    /**
     * Get share counter.
     *
     * @return {object} a share counter
     */
    getShareCounter: function() {
      // Variables
      const script = document.createElement("script");
      const callback = getCallbackName("vue_goodshare", 9999, 111);

      // Create `script` tag with share count URL
      script.src = `https://api.pinterest.com/v1/urls/count.json?url=${encodeURIComponent(
        this.$props.page_url
      )}&callback=${callback}`;

      // Add `script` tag with share count URL
      // to end of `body` tag
      document.body.appendChild(script);

      // Set share count to `counter_pinterest` v-model
      window[callback] = count => {
        if (count) {
          this.counter_pinterest =
            count.count >= 1000 ? sliceThousandInt(count.count) : count.count;
        }
      };
    }
  },
  mounted() {
    // Show share counter when page loaded
    if (this.$props.has_counter) this.getShareCounter();
  }
};
</script>

<style scoped lang="scss">
// Fontello
@font-face {
  font-family: "Fontello";
  src: url("../fonts/fontello.eot?26533562");
  src: url("../fonts/fontello.eot?26533562#iefix") format("embedded-opentype"),
    url("../fonts/fontello.woff2?26533562") format("woff2"),
    url("../fonts/fontello.woff?26533562") format("woff"),
    url("../fonts/fontello.ttf?26533562") format("truetype"),
    url("../fonts/fontello.svg?26533562#fontello") format("svg");
  font-weight: normal;
  font-style: normal;
}

[class^="icon-"]:before,
[class*=" icon-"]:before {
  font-family: "Fontello";
  font-style: normal;
  font-weight: normal;
  speak: none;
  display: inline-block;
  text-decoration: inherit;
  width: 1em;
  margin-right: 0.2em;
  text-align: center;
  font-variant: normal;
  text-transform: none;
  line-height: 1em;
  margin-left: 0.2em;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-pinterest:before {
  content: "\e803";
}

// Colors
$pinterest_main_color: rgb(189, 8, 28);
$pinterest_main_color_opacity: rgba(189, 8, 28, 0.5);
$gradient_color: rgb(219, 38, 58);
$background_white_color: rgb(254, 254, 254);
$text_white_color: rgb(254, 254, 254);
$text_white_color_opacity: rgba(254, 254, 254, 0.5);

// Reset
.button-social * {
  box-sizing: border-box;
}

// Button Social link style
.button-social {
  display: inline-flex;
  cursor: pointer;
  padding: 7px 10px;
  margin: 3px 1.5px;
  border-radius: 3px;
  -moz-border-radius: 3px;
  -webkit-border-radius: 3px;
}

// Button Social link style on hover
.button-social:hover {
  opacity: 0.9;
}

// Button Social round edges
.button-social__square_edges {
  border-radius: 0;
  -moz-border-radius: 0;
  -webkit-border-radius: 0;
}

// Button pinterest style `flat`
.pinterest__design__flat {
  background-color: $pinterest_main_color;
  color: $text_white_color;
}

// Button pinterest style `gradient`
.pinterest__design__gradient {
  background-image: linear-gradient(
    to bottom,
    $pinterest_main_color,
    $gradient_color
  );
  background-image: -moz-linear-gradient(
    to bottom,
    $pinterest_main_color,
    $gradient_color
  );
  background-image: -o-linear-gradient(
    to bottom,
    $pinterest_main_color,
    $gradient_color
  );
  background-image: -webkit-linear-gradient(
    to bottom,
    $pinterest_main_color,
    $gradient_color
  );
  background-image: -ms-linear-gradient(
    to bottom,
    $pinterest_main_color,
    $gradient_color
  );
  color: $text_white_color;
}

// Button pinterest style `outline`
.pinterest__design__outline {
  background-color: $background_white_color;
  border: 1px solid $pinterest_main_color;
  color: $pinterest_main_color;
}

// Title
.title-social {
  margin-left: 0;
}

// Counter
.counter-pinterest {
  margin-left: 6px;
  padding-left: 6px;
}

.pinterest__design__flat .counter-pinterest,
.pinterest__design__gradient .counter-pinterest {
  border-left: 1px solid $text_white_color_opacity;
}

.pinterest__design__outline .counter-pinterest {
  border-left: 1px solid $pinterest_main_color_opacity;
}
</style>
