<template>
  <a
    class="button-social"
    :class="buttonSocialDesignObject"
    :page-url="page_url"
    :page-title="page_title"
    :page-description="page_description"
    :button-design="button_design"
    :title-social="title_social"
    :has-icon="has_icon"
    :has-square-edges="has_square_edges"
    :has-counter="has_counter"
    @click.prevent="showShareWindow"
  >
    <i class="icon-linkedin" v-if="this.$props.has_icon"></i>
    <span class="title-social" v-if="this.$props.title_social">
      {{ title_social }}
    </span>
    <span
      class="counter-linkedin"
      v-if="this.$props.has_counter"
    >
      {{ counter_linkedin }}
    </span>
  </a>
</template>

<script>
import { clickEvent } from "../helpers/events";
import { documentHref } from "../helpers/href";
import { documentTitle } from "../helpers/title";
import { metaDescription } from "../helpers/description";
import { getCallbackName } from "../helpers/callback_name";
import { sliceThousandInt } from "../helpers/count_number";
import { openPopUpWindow } from "../helpers/popup_window";

export default {
  name: "VueGoodshareLinkedIn",
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
        linkedin__design__flat: this.$props.button_design === "flat",
        linkedin__design__gradient: this.$props.button_design === "gradient",
        linkedin__design__outline: this.$props.button_design === "outline"
      },
      counter_linkedin: 0
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
      const share_url = `https://www.linkedin.com/shareArticle?url=${encodeURIComponent(
        this.$props.page_url
      )}&text=${encodeURIComponent(
        this.$props.page_title
      )}&summary=${encodeURIComponent(this.$props.page_description)}&mini=true`;

      // onClick event
      clickEvent(this, "linkedin");

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
      script.src = `https://www.linkedin.com/countserv/count/share?url=${encodeURIComponent(
        this.$props.page_url
      )}&callback=${callback}`;

      // Add `script` tag with share count URL
      // to end of `body` tag
      document.body.appendChild(script);

      // Set share count to `counter_linkedin` v-model
      window[callback] = count => {
        if (count) {
          this.counter_linkedin =
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

.icon-linkedin:before {
  content: "\e808";
}

// Colors
$linkedin_main_color: rgb(0, 119, 181);
$linkedin_main_color_opacity: rgba(0, 119, 181, 0.5);
$gradient_color: rgb(30, 149, 211);
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

// Button linkedin style `flat`
.linkedin__design__flat {
  background-color: $linkedin_main_color;
  color: $text_white_color;
}

// Button linkedin style `gradient`
.linkedin__design__gradient {
  background-image: linear-gradient(
    to bottom,
    $linkedin_main_color,
    $gradient_color
  );
  background-image: -moz-linear-gradient(
    to bottom,
    $linkedin_main_color,
    $gradient_color
  );
  background-image: -o-linear-gradient(
    to bottom,
    $linkedin_main_color,
    $gradient_color
  );
  background-image: -webkit-linear-gradient(
    to bottom,
    $linkedin_main_color,
    $gradient_color
  );
  background-image: -ms-linear-gradient(
    to bottom,
    $linkedin_main_color,
    $gradient_color
  );
  color: $text_white_color;
}

// Button linkedin style `outline`
.linkedin__design__outline {
  background-color: $background_white_color;
  border: 1px solid $linkedin_main_color;
  color: $linkedin_main_color;
}

// Title
.title-social {
  margin-left: 3px;
}

// Counter
.counter-linkedin {
  margin-left: 6px;
  padding-left: 6px;
}

.linkedin__design__flat .counter-linkedin,
.linkedin__design__gradient .counter-linkedin {
  border-left: 1px solid $text_white_color_opacity;
}

.linkedin__design__outline .counter-linkedin {
  border-left: 1px solid $linkedin_main_color_opacity;
}
</style>
