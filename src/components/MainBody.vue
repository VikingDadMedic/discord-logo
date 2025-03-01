<template>
  <div>
    <header>
      <h1>vsLogomate</h1>
      <p>Below is a code generator:</p>
    </header>
    <portal-target name="destination" />
    <section>
      <div class="left">
        <vueSlider
          ref="vueSlider"
          v-model="size"
          :reverse="true"
          direction="vertical"
          :width="4"
          :height="482"
          :min="18"
          :max="250"
        />
        <div class="stack">
          <div class="input">
            <toggle-button
              v-model="isLeft"
              class="button"
              :color="{ checked: '#76DA72', unchecked: '#DA7272' }"
              :disabled="previewDiscordType != 'corner'"
              :labels="{ checked: 'left', unchecked: 'right' }"
              :width="100"
            />
            <toggle-button
              v-model="isTop"
              class="button"
              :color="{ checked: '#7289DA', unchecked: '#D4DA72' }"
              :disabled="previewDiscordType != 'corner'"
              :labels="{ checked: 'top', unchecked: 'bottom' }"
              :width="100"
            />
            <toggle-button
              v-model="isClickable"
              class="button"
              :color="{ checked: '#2BA027', unchecked: '#B9B6B6' }"
              :sync="true"
              :disabled="true"
              :labels="{ checked: 'clickable link', unchecked: 'no link' }"
              :width="100"
            />
            <input
              v-model="customLink"
              :size="30"
              class="text"
              placeholder="insert link (e.g. discord invite link)"
            />
            <input
              v-model="bubbleText"
              :size="30"
              class="text"
              placeholder="insert text"
            />
          </div>

          <portal
            to="destination"
            :disabled="previewDiscordType != 'speechbubble'"
          >
            <div id="preview" ref="preview">
              <DiscordLogo
                v-if="previewDiscordType == 'standard'"
                :discord-eyes="discordEyes"
                :background="background"
                :is-rainbow="isRainbow"
                :animation-style="animationStyle"
                :custom-link="customLink"
                class="previewbox"
                :width="size"
                :height="size"
                :discordfill="colors.discordfill.hex"
                :discordcolor="colors.discordcolor.hex"
              />
              <DiscordText
                :discord-eyes="discordEyes"
                :background="background"
                v-else-if="previewDiscordType == 'speechbubble'"
                :is-rainbow="isRainbow"
                :animation-style="animationStyle"
                :style="getDTextStyle"
                ref="discordtext"
                :custom-link="customLink"
                :height="size / 4"
                @htmlModified="onHtmlModified"
                :standard-text="bubbleText"
                :discordfill="colors.discordfill.hex"
                :discordcolor="colors.discordcolor.hex"
                class="previewbox previewDiscordText"
                :write-text="false"
              />
              <DiscordCorner
                v-else
                :discord-eyes="discordEyes"
                :custom-link="customLink"
                :is-rainbow="isRainbow"
                :animation-style="animationStyle"
                :style="getDCornerStyle"
                class="previewbox"
                :width="size"
                :height="size"
                :discordfill="colors.discordfill.hex"
                :discordcolor="colors.discordcolor.hex"
              />
            </div>
          </portal>
        </div>
      </div>
      <div class="color-picker">
        <chrome-picker v-model="colors.discordcolor" />
        <chrome-picker v-model="colors.discordfill" />
      </div>
      <textarea v-model="previewCode" />
    </section>
    <footer>
      <p>This project is neither built nor endorsed by Discord.</p>
    </footer>
  </div>
</template>

<script>
import DiscordLogo from "../components/DiscordLogo.vue";
import DiscordCorner from "../components/DiscordCorner.vue";
import vueSlider from "vue-slider-component";
import DiscordText from "../components/DiscordText.vue";
import { Chrome } from "vue-color";

export default {
  name: "MainBody",
  components: {
    DiscordText,
    DiscordLogo,
    DiscordCorner,
    vueSlider,
    "chrome-picker": Chrome
  },
  props: {
    previewDiscordType: {
      type: String,
      default: "corner"
    },
    animationStyle: {
      type: String,
      default: "swirl"
    },
    isRainbow: {
      type: Boolean,
      default: false
    },
    discordEyes: {
      type: String,
      default: "none" //none wink angry noeyes
    },
    background: {
      type: String,
      default: "none"
    }
  },
  data() {
    return {
      bubbleText: "Join us on Discord!",
      previewCode: "",
      size: 100,
      isLeft: true,
      isTop: true,
      rotation: "",

      colors: {
        discordcolor: {
          hex: "#FFFFFF",
          hsl: {
            h: 144,
            s: 0,
            l: 1,
            a: 1
          },
          hsv: {
            h: 90,
            s: 0,
            v: 0,
            a: 1
          },
          rgba: {
            r: 255,
            g: 255,
            b: 255,
            a: 1
          },
          a: 1
        },
        discordfill: {
          hex: "#7289DA",
          hsl: {
            h: 227,
            s: 0.58,
            l: 0.65,
            a: 1
          },
          hsv: {
            h: 0,
            s: 0,
            v: 0,
            a: 1
          },
          rgba: {
            r: 114,
            g: 137,
            b: 218,
            a: 1
          },
          a: 1
        }
      },
      customLink: ""
    };
  },
  computed: {
    getDTextStyle: {
      get: function() {
        return { backgroundColor: this.colors.discordfill.hex };
      }
    },
    getDCornerStyle: {
      get: function() {
        var returnThis = {
          transform: "rotate(0deg)",
          backgroundColor: "#FFFFFF"
        };
        if (this.colors.discordfill.hex == "#FFFFFF") {
          returnThis.backgroundColor = this.colors.discordcolor.hex;
        } else {
          returnThis.backgroundColor = "#FFFFFF";
        }
        if (!this.isLeft && this.isTop) {
          this.rotation = "transform: rotateY(180deg);";
          returnThis.transform = "rotateY(180deg)";
          return returnThis;
        } else if (!this.isLeft && !this.isTop) {
          this.rotation = "transform: rotateY(180deg) rotateX(180deg);";
          returnThis.transform = "rotateY(180deg) rotateX(180deg)";
          return returnThis;
        } else if (this.isLeft && !this.isTop) {
          this.rotation = "transform: rotateX(180deg);";
          returnThis.transform = "rotateX(180deg)";
          return returnThis;
        } else {
          return returnThis;
        }
      }
    },
    isClickable: {
      get: function() {
        return this.customLink == "" ? false : true;
      }
    }
  },
  watch: {
    previewDiscordType: function() {
      this.updatePreviewCode();
    },
    isRainbow: function() {
      this.updatePreviewCode();
    },
    size: function() {
      this.updatePreviewCode();
    },
    isLeft: function() {
      this.updatePreviewCode();
    },
    isTop: function() {
      this.updatePreviewCode();
    },
    colors: {
      handler: function() {
        this.updatePreviewCode();
      },
      deep: true
    },
    customLink: function() {
      this.updatePreviewCode();
    }
  },
  created: function() {
    this.$nextTick(function() {
      this.updatePreviewCode();
    });
  },
  methods: {
    refreshSlider: function() {
      this.$nextTick(function() {
        this.$refs.vueSlider.refresh();
      });
    },
    onHtmlModified: function() {
      this.updatePreviewCode();
    },
    changeColor: function(discordcolor, discordfill) {
      this.colors.discordcolor = { hex: discordcolor, a: 1 };
      this.colors.discordfill = { hex: discordfill, a: 1 };
    },
    //changeBackground: function (background) {
    //  this.background = background
    //},
    updatePreviewCode: function() {
      this.$nextTick(function() {
        // => 'DOM loaded and ready'
        if (this.$refs.preview == null) {
          this.previewCode = "";
        } else {
          this.previewCode = this.$refs.preview.innerHTML;
          var styleCode =
            "* { font-family: 'Avenir', Helvetica, Arial, sans-serif; } .discord-logo { transform: scale(0.7); transform-origin: 24px 24px; } ";
          // this.previewDiscordType     //corner standard speechbubble
          // this.animationStyle         //shake swirl rotateX rotateY
          if (this.previewDiscordType == "corner") {
            styleCode +=
              ".discord-corner { transform: rotate(-45deg); transform-origin: 60px 60px; }";
          } else if (this.previewDiscordType == "speechbubble") {
            styleCode +=
              ".speechbubble { position: relative; transform: translateY(-50%); } .discordtext { opacity: 0.75; } .discordtext:hover { opacity: 1; } .discordtext a { text-decoration: none; }";
          }
          if (this.animationStyle == "rotateX") {
            styleCode +=
              ".discord-logo.rotateX-animation .discord-original { transition: transform 300ms linear; transform-origin: 50% 50%; } .discord-logo-container:hover .rotateX-animation .discord-original, .animated .rotateX-animation .discord-original { transform: rotateX(360deg); }";
          } else if (this.animationStyle == "rotateY") {
            styleCode +=
              ".discord-logo.rotateY-animation .discord-original { transition: transform 300ms linear; transform-origin: 50% 50%; } .discord-logo-container:hover .rotateY-animation .discord-original, .animated .rotateY-animation .discord-original { transform: rotateY(180deg); }";
          } else if (this.animationStyle == "swirl") {
            styleCode +=
              ".discord-logo.swirl-animation .discord-outer-layer { transition: transform 800ms cubic-bezier(0.7, 1, 0.7, 1); transform-origin: 50% 50%; } .discord-logo-container:hover .swirl-animation .discord-outer-layer, .animated .swirl-animation .discord-outer-layer { transform: scale(1.5) rotate(360deg); } .discord-logo.swirl-animation .discord-middle-layer { transition: transform 800ms cubic-bezier(0.5, 1, 0.5, 1); transform-origin: 50% 50%; } .discord-logo-container:hover .swirl-animation .discord-middle-layer, .animated .swirl-animation .discord-middle-layer { transform: scale(1.4) rotate(360deg); } .discord-logo.swirl-animation .discord-inner-layer { transition: transform 800ms cubic-bezier(0.3, 1, 0.3, 1); transform-origin: 50% 50%; } .discord-logo-container:hover .swirl-animation .discord-inner-layer, .animated .swirl-animation .discord-inner-layer { transform: scale(1.3) rotate(360deg); } .discord-logo.swirl-animation .discord-original { transition: visibility 0ms; transition-delay: 800ms; } .discord-logo-container:hover .swirl-animation .discord-original, .animated .swirl-animation .discord-original { visibility: hidden; transition-delay: 0ms; }";
          } else if (this.animationStyle == "shake") {
            styleCode +=
              ".discord-logo.shake-animation .discord-original { } .discord-logo-container:hover .shake-animation .discord-original, .animated .shake-animation .discord-original { animation-name:shake; animation-duration:100ms; animation-timing-function:ease-in-out; animation-iteration-count:infinite } @keyframes shake { 2% {transform:translate(.5px, 1.5px) rotate(1.5deg)} 4% {transform:translate(.5px, 1.5px) rotate(1.5deg)} 6% {transform:translate(-1.5px, -1.5px) rotate(-.5deg)} 8% {transform:translate(.5px, -.5px) rotate(.5deg)} 10% {transform:translate(.5px, 2.5px) rotate(.5deg)} 12% {transform:translate(2.5px, 1.5px) rotate(-.5deg)} 14% {transform:translate(-1.5px, 2.5px) rotate(-.5deg)} 16% {transform:translate(-.5px, .5px) rotate(.5deg)} 18% {transform:translate(.5px, 2.5px) rotate(1.5deg)} 20% {transform:translate(-.5px, -.5px) rotate(.5deg)} 22% {transform:translate(2.5px, .5px) rotate(-.5deg)} 24% {transform:translate(-1.5px, -1.5px) rotate(.5deg)} 26% {transform:translate(2.5px, -.5px) rotate(-.5deg)} 28% {transform:translate(1.5px, -.5px) rotate(.5deg)} 30% {transform:translate(.5px, .5px) rotate(-.5deg)} 32% {transform:translate(-1.5px, .5px) rotate(-.5deg)} 34% {transform:translate(.5px, 2.5px) rotate(-.5deg)} 36% {transform:translate(-.5px, -.5px) rotate(1.5deg)} 38% {transform:translate(-1.5px, -1.5px) rotate(.5deg)} 40% {transform:translate(-1.5px, 1.5px) rotate(1.5deg)} 42% {transform:translate(.5px, -1.5px) rotate(1.5deg)} 44% {transform:translate(.5px, .5px) rotate(.5deg)} 46% {transform:translate(-1.5px, -1.5px) rotate(1.5deg)} 48% {transform:translate(.5px, -1.5px) rotate(.5deg)} 50% {transform:translate(2.5px, .5px) rotate(-.5deg)} 52% {transform:translate(-.5px, 2.5px) rotate(-.5deg)} 54% {transform:translate(.5px, .5px) rotate(.5deg)} 56% {transform:translate(-1.5px, 2.5px) rotate(.5deg)} 58% {transform:translate(2.5px, .5px) rotate(.5deg)} 60% {transform:translate(-1.5px, 2.5px) rotate(.5deg)} 62% {transform:translate(1.5px, -.5px) rotate(-.5deg)} 64% {transform:translate(1.5px, -1.5px) rotate(1.5deg)} 66% {transform:translate(1.5px, -1.5px) rotate(-.5deg)} 68% {transform:translate(.5px, 2.5px) rotate(-.5deg)} 70% {transform:translate(1.5px, -1.5px) rotate(1.5deg)} 72% {transform:translate(1.5px, 1.5px) rotate(-.5deg)} 74% {transform:translate(-.5px, 1.5px) rotate(1.5deg)} 76% {transform:translate(1.5px, 2.5px) rotate(.5deg)} 78% {transform:translate(-.5px, .5px) rotate(.5deg)} 80% {transform:translate(-1.5px, 2.5px) rotate(.5deg)} 82% {transform:translate(.5px, 2.5px) rotate(-.5deg)} 84% {transform:translate(2.5px, -.5px) rotate(.5deg)} 86% {transform:translate(1.5px, .5px) rotate(.5deg)} 88% {transform:translate(-.5px, -1.5px) rotate(-.5deg)} 90% {transform:translate(1.5px, -.5px) rotate(1.5deg)} 92% {transform:translate(.5px, 2.5px) rotate(.5deg)} 94% {transform:translate(2.5px, .5px) rotate(-.5deg)} 96% {transform:translate(.5px, 2.5px) rotate(.5deg)} 98% {transform:translate(2.5px, -1.5px) rotate(1.5deg)} 0%,100% {transform:translate(0, 0) rotate(0)} }";
          } else if (this.animationStyle == "softshake") {
            styleCode +=
              ".discord-logo.softshake-animation .discord-original { transform-origin: 24px 24px; } .discord-logo-container:hover .softshake-animation .discord-original, .animated .softshake-animation .discord-original { animation: softshake 2000ms linear forwards; } @keyframes softshake { 0%,66%,100% {transform:rotate( 0deg)} 3% {transform:rotate(-18.0deg)} 6% {transform:rotate( 14.4deg)} 9% {transform:rotate(-11.5deg)} 12% {transform:rotate( 9.21deg)} 15% {transform:rotate(-7.37deg)} 18% {transform:rotate( 5.89deg)} 21% {transform:rotate(-4.71deg)} 24% {transform:rotate( 3.77deg)} 27% {transform:rotate(-3.02deg)} 30% {transform:rotate( 2.41deg)} 33% {transform:rotate(-1.93deg)} 36% {transform:rotate( 1.54deg)} 39% {transform:rotate(-1.23deg)} 42% {transform:rotate( 0.99deg)} 45% {transform:rotate(-0.79deg)} 48% {transform:rotate( 0.63deg)} 51% {transform:rotate(-0.50deg)} 54% {transform:rotate( 0.40deg)} 57% {transform:rotate(-0.32deg)} 60% {transform:rotate( 0.25deg)} 63% {transform:rotate(-0.20deg)} }";
          }
          this.previewCode +=
            "\n\n<style type='text/css'>" + styleCode + "</style>";
          switch (this.background) {
            case "starfield":
              this.previewCode +=
                `\n\n<script>var discordCanvas = document.getElementById("discordCanvas");var discordCtx = discordCanvas.getContext("2d");var discordFrames = 0;(function drawDiscordAnimation () {discordCanvas.style.width = "50px";discordCanvas.style.height = "50px";discordCanvas.width = 250;discordCanvas.height = 250;discordCtx.fillStyle="#fff8";for(let j=250, w=100, p=0; j--;){let Z=1-(j*j/w+discordFrames/100)%1;let s=1+Math.pow(5*(1-Z),2)/2;discordCtx.beginPath();discordCtx.arc(w+(99-j%199)/Z,100+(99-j*j*7%198)/Z,s,0,7);discordCtx.fill()};discordFrames++;requestAnimationFrame(drawDiscordAnimation)}());<` +
                "/script>";
              break;
            case "grid":
              this.previewCode +=
                `\n\n<script>var discordCanvas = document.getElementById("discordCanvas");var discordCtx = discordCanvas.getContext("2d");var discordFrames = 0;(function drawDiscordAnimation () {discordCanvas.style.width = "50px";discordCanvas.style.height = "50px";discordCanvas.width = 250;discordCanvas.height = 250;discordCtx.strokeStyle="#fff4";var w=120,i=200,t=discordFrames/70,X=0,Z=0;var r=q=>discordCtx[q?"lineTo":"moveTo"](w+(X-7)/Z*w*2,2/Z*w);for(;i--;discordCtx.lineWidth=0.5+8/(1+Z),r(Z++),r(X--),discordCtx.stroke())discordCtx.beginPath(),X=(i+Math.sin(t)*4)%14,Z=1+(i/14|0)-t*5%1,r();discordFrames++;requestAnimationFrame(drawDiscordAnimation)}());<` +
                "/script>";
              break;
            case "rush":
              this.previewCode +=
                `\n\n<script>var discordCanvas = document.getElementById("discordCanvas");var discordCtx = discordCanvas.getContext("2d");var discordFrames = 0;(function drawDiscordAnimation () {discordCanvas.style.width = "50px";discordCanvas.style.height = "50px";discordCanvas.width = 250;discordCanvas.height = 250;discordCtx.fillStyle="#fff3";var W=120,j=10,i=0,p=0,V=0,Z=0,s=0,t=discordFrames/200; for(;--j;)for(i=16;i--;discordCtx.fillRect(W+Math.sin(p=.39*i+j/8-6.03*V-Math.sin(t*2)*3)/Z*W-s/2,120+Math.cos(p)/Z*W-s/2,s,s))Z=.5+j/2-t*6+(V=(t*6)|0),s=200/(1+Z)/(1+Z)/(1+Z);discordFrames++;requestAnimationFrame(drawDiscordAnimation)}());<` +
                "/script>";
              break;
          }
        }
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.debug {
  height: 500px;
}

header {
  padding-right: 20px;
  padding-left: 20px;
  padding-top: 50px;
  padding-bottom: 15px;
  max-width: 580px;
  margin: auto;
  text-align: left;
}

#preview {
  display: inline-block;
}

.previewbox {
  margin-top: 8px;
  display: inline-block;
}
.previewDiscordText {
  padding-right: 20px;
}
section {
  padding-top: 50px;
  padding-right: 20px;
  padding-left: 50px;
  padding-bottom: 50px;
  max-width: 1200px;
  margin: auto;
  display: flex;
}
.left {
  height: 482px;
  display: flex;
}
.left .stack {
  text-align: left;
  width: 262px;
}

.button,
.input input {
  margin-bottom: 5px;
}

textarea {
  margin-left: 5px;
  width: 100%;
}
footer {
  font-size: 15px;
  padding-bottom: 5px;
}
foreignObject {
  width: 100%;
  height: 100%;
}
</style>
