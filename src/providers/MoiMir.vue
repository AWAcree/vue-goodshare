<template>
  <a
    class="button-social"
    :class="buttonSocialDesignObject"
    :page-url="page_url"
    :page-title="page_title"
    :page-description="page_description"
    :page-image="page_image"
    :button-design="button_design"
    :title-social="title_social"
    :has-square-edges="has_square_edges"
    :has-icon="has_icon"
    :has-counter="has_counter"
    @click.prevent="showShareWindow"
  >
    <i class="icon-moimir" v-if="this.$props.has_icon"></i>
    <span class="title-social" v-if="this.$props.title_social">
      {{ title_social }}
    </span>
    <span
      class="counter-moimir"
      v-if="this.$props.has_counter"
    >
      {{ counter_moimir }}
    </span>
  </a>
</template>

<script>
import { clickEvent } from "../helpers/events";
import { documentHref } from "../helpers/href";
import { documentTitle } from "../helpers/title";
import { metaDescription } from "../helpers/description";
import { linkAppleTouchIcon } from "../helpers/icon";
import { getCallbackName } from "../helpers/callback_name";
import { sliceThousandInt } from "../helpers/count_number";
import { openPopUpWindow } from "../helpers/popup_window";

export default {
  name: "VueGoodshareMoiMir",
  props: {
    page_url: {
      type: String,
      default: documentHref
    },
    page_title: {
      type: String,
      default: documentTitle
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
        moimir__design__flat: this.$props.button_design === "flat",
        moimir__design__gradient: this.$props.button_design === "gradient",
        moimir__design__outline: this.$props.button_design === "outline"
      },
      counter_moimir: 0
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
      const share_url = `https://connect.mail.ru/share?url=${encodeURIComponent(
        this.$props.page_url
      )}&title=${encodeURIComponent(
        this.$props.page_title
      )}&description=${encodeURIComponent(
        this.$props.page_description
      )}&imageurl=${encodeURIComponent(this.$props.page_image)}`;

      // onClick event
      clickEvent(this, "moimir");

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
      script.src = `https://appsmail.ru/share/count/${encodeURIComponent(
        this.$props.page_url.replace(/^.*?:\/\//, "")
      )}?callback=${callback}`;

      // Add `script` tag with share count URL
      // to end of `body` tag
      document.body.appendChild(script);

      // Set share count to `counter_moimir` v-model
      window[callback] = count => {
        if (count) {
          this.counter_moimir =
            count.share_mm >= 1000
              ? sliceThousandInt(count.share_mm)
              : count.share_mm;
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

.icon-moimir:before {
  content: "\e80e";
}

// Colors
$moimir_main_color: rgb(22, 141, 226);
$moimir_main_color_opacity: rgba(22, 141, 226, 0.5);
$gradient_color: rgb(42, 161, 246);
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

// Button moimir style `flat`
.moimir__design__flat {
  background-color: $moimir_main_color;
  color: $text_white_color;
}

// Button moimir style `gradient`
.moimir__design__gradient {
  background-image: linear-gradient(
    to bottom,
    $moimir_main_color,
    $gradient_color
  );
  background-image: -moz-linear-gradient(
    to bottom,
    $moimir_main_color,
    $gradient_color
  );
  background-image: -o-linear-gradient(
    to bottom,
    $moimir_main_color,
    $gradient_color
  );
  background-image: -webkit-linear-gradient(
    to bottom,
    $moimir_main_color,
    $gradient_color
  );
  background-image: -ms-linear-gradient(
    to bottom,
    $moimir_main_color,
    $gradient_color
  );
  color: $text_white_color;
}

// Button moimir style `outline`
.moimir__design__outline {
  background-color: $background_white_color;
  border: 1px solid $moimir_main_color;
  color: $moimir_main_color;
}

// Title
.title-social {
  margin-left: 5px;
}

// Counter
.counter-moimir {
  margin-left: 6px;
  padding-left: 6px;
}

.moimir__design__flat .counter-moimir,
.moimir__design__gradient .counter-moimir {
  border-left: 1px solid $text_white_color_opacity;
}

.moimir__design__outline .counter-moimir {
  border-left: 1px solid $moimir_main_color_opacity;
}
</style>
