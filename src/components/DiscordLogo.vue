<template>
  <svg
    ref="discordLogoRootElement"
    :color="discordcolor"
    :fill="discordfill"
    :width="width"
    :height="height"
    class="discord-logo-container"
    viewBox="0 0 48 48"
  >
    <rect width="100%" height="100%" fill="currentfill" />
    <foreignObject v-if="background !== 'none'" style="width:100%; height:100%">
      <body xmlns="http://www.w3.org/1999/xhtml">
        <canvas :ref="canvasID" id="discordCanvas" />
      </body>
    </foreignObject>
    <defs>
      <g>
        <defs>
          <path
            id="discord-def-face"
            fill="currentcolor"
            d="M7.36515 14.1075C15.2213 30.8276 152.704 284.241 155.159 286.331C157.123 288.421 182.165 241.918 182.165 236.171C182.165 230.946 67.2683 17.765 58.4302 6.27002C55.4841 2.61251 42.2268 0 27.0055 0H0L7.36515 14.1075Z"
          />
          <g id="discord-def-face-eyes">
            <path
              id="discord-def-face-left-eye"
              fill="currentfill"
              d="M104.094 11.4949C109.986 24.0349 202.296 194.893 204.26 197.505C206.224 200.118 231.265 150.48 230.774 144.21C230.774 141.075 213.589 107.635 192.966 70.5376L155.159 2.61237L126.68 1.04487C98.2015 -0.522638 98.2015 -0.522638 104.094 11.4949Z"
            />
            <path
              id="discord-def-face-right-eye"
              fill="currentfill"
              d="M317.684 65.3124C300.007 98.23 285.277 127.49 285.277 130.625C285.277 134.283 294.606 135.85 310.809 134.805L336.833 133.238L355 99.7975C364.82 81.51 375.623 63.7449 378.569 60.6099C385.443 53.2949 418.832 52.2499 416.868 60.0874C415.885 63.2224 403.119 88.3025 388.389 116.518L361.383 167.2H261.708L219.482 246.62L177.255 325.518L214.571 395.533C235.194 434.198 253.361 463.981 254.834 462.936C255.816 461.368 281.349 415.911 310.809 362.093L363.838 263.863L338.797 262.295C324.558 261.25 311.3 262.818 308.845 265.43C305.899 268.565 293.624 289.988 280.367 314.023C267.6 338.058 255.325 357.391 253.361 356.868C251.397 356.868 245.505 349.553 240.595 341.193L232.248 326.563L260.235 274.313L288.714 222.063L389.862 216.838L443.382 117.563C473.334 62.6999 498.375 15.1523 499.848 11.4948C501.812 6.79228 485.118 5.22477 426.197 5.22477H350.581L317.684 65.3124Z"
            />
          </g>
        </defs>
        <g :id="discordFaceID">
          <!--id="discordLogoID"-->
          <use href="#discord-def-face" />
          <g id="discord-logo-eyes">
            <mask id="mask-right-eye-wink">
              <ellipse fill="#FFFFFF" ry="15" rx="15" cy="39.7" cx="35" />
              <ellipse fill="#000000" ry="15" rx="15" cy="40.5" cx="34" />
            </mask>
            <mask id="mask-eyes-angry">
              <rect height="48" width="48" y="0" x="0" fill="#FFFFFF" />
              <rect
                transform="rotate(45 24,14.5)"
                height="24"
                width="24"
                y="2.5"
                x="12"
                fill="#000000"
              />
            </mask>
            <g class="discord-eyes" />
          </g>
        </g>
        <mask id="mask-outer-layer">
          <rect width="100%" height="100%" fill="#FFFFFF" />
          <circle r="42%" cx="50%" cy="50%" fill="#000000" />
        </mask>
        <mask id="mask-middle-layer">
          <rect width="100%" height="100%" fill="#000000" />
          <circle r="43%" cx="50%" cy="50%" fill="#FFFFFF" />
          <circle r="32%" cx="50%" cy="50%" fill="#000000" />
        </mask>
        <mask id="mask-inner-layer">
          <rect width="100%" height="100%" fill="#000000" />
          <circle r="32%" cx="50%" cy="50%" fill="#FFFFFF" />
        </mask>
      </g>
    </defs>
    <g class="discord-logo" />
    <a v-if="customLink" :href="customLink">
      <rect width="100%" height="100%" fill-opacity="0" />
    </a>
    <animate
      v-if="isRainbow"
      fill="freeze"
      dur="4000ms"
      begin="0s"
      values="#DA7272;#DABF72;#A6DA72;#72DA8C;#72DADA;#728CDA;#A672DA;#DA72C0;#DA7272"
      calMode="linear"
      attributeName="color"
      repeatCount="indefinite"
    />
  </svg>
</template>

<script>
export default {
  //TODO: add animationStyle and angry/wink discord logo via JavaScript! (define a root element, get ID from there)
  name: "DiscordLogo",
  props: {
    width: {
      type: Number,
      default: 300
    },
    height: {
      type: Number,
      default: 300
    },
    discordcolor: {
      type: String,
      default: "#7289DA"
    },
    discordfill: {
      type: String,
      default: "#23272A"
    },
    customLink: {
      type: String,
      default: ""
    },
    animationStyle: {
      type: String,
      default: "swirl" //swirl rotateX rotateY shake softshake
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
      discordFaceID: null,
      canvasID: null,
      canvas: "",
      ctx: "",
      frames: 0,
      RAF: null
    };
  },
  watch: {
    animationStyle: function() {
      this.updateAnimation();
    },
    discordEyes: function() {
      this.updateEyes();
    },
    background: function() {
      this.wireUpCanvas();
    }
  },
  mounted() {
    this.discordFaceID = "discordFaceID" + this._uid;
    this.wireUpCanvas();
  },
  created: function() {
    this.update();
  },
  methods: {
    update: function() {
      this.updateAnimation();
      this.updateEyes();
    },
    updateAnimation: function() {
      this.$nextTick(function() {
        var svgns = "http://www.w3.org/2000/svg";
        var xlinkns = "http://www.w3.org/1999/xlink";
        var discordLogoRootElement = this.$refs.discordLogoRootElement;
        var discordLogo = discordLogoRootElement.getElementsByClassName(
          "discord-logo"
        )[0];
        while (discordLogo.firstChild) {
          discordLogo.removeChild(discordLogo.firstChild);
        }
        discordLogo.setAttribute(
          "class",
          "discord-logo " + this.animationStyle + "-animation"
        );
        var useElement = document.createElementNS(svgns, "use");
        useElement.setAttribute("class", "discord-original");
        useElement.setAttributeNS(xlinkns, "href", "#" + this.discordFaceID);
        discordLogo.appendChild(useElement);
        if (this.animationStyle == "swirl") {
          ["inner", "middle", "outer"].map(function(item) {
            var useElement = document.createElementNS(svgns, "use");
            useElement.setAttribute("class", "discord-" + item + "-layer");
            useElement.setAttributeNS(
              xlinkns,
              "href",
              "#" + this.discordFaceID
            );
            useElement.setAttribute("mask", "url(#mask-" + item + "-layer)");
            discordLogo.appendChild(useElement);
          }, this);
        }
      });
    },
    updateEyes: function() {
      this.$nextTick(function() {
        var svgns = "http://www.w3.org/2000/svg";
        var xlinkns = "http://www.w3.org/1999/xlink";
        var discordLogoRootElement = this.$refs.discordLogoRootElement;
        var discordEyesSVG = discordLogoRootElement.getElementsByClassName(
          "discord-eyes"
        )[0];
        while (discordEyesSVG.firstChild) {
          discordEyesSVG.removeChild(discordEyesSVG.firstChild);
        }
        if (this.discordEyes == "angry") {
          var useElement = document.createElementNS(svgns, "use");
          useElement.setAttributeNS(xlinkns, "href", "#discord-def-face-eyes");
          useElement.setAttribute("mask", "url(#mask-eyes-angry)");
          discordEyesSVG.appendChild(useElement);
        } else if (this.discordEyes == "wink") {
          var useElement = document.createElementNS(svgns, "use");
          useElement.setAttributeNS(
            xlinkns,
            "href",
            "#discord-def-face-left-eye"
          );
          discordEyesSVG.appendChild(useElement);
          var useElement = document.createElementNS(svgns, "use");
          useElement.setAttributeNS(
            xlinkns,
            "href",
            "#discord-def-face-right-eye"
          );
          useElement.setAttribute("mask", "url(#mask-right-eye-wink)");
          discordEyesSVG.appendChild(useElement);
        } else if (this.discordEyes == "none") {
          var useElement = document.createElementNS(svgns, "use");
          useElement.setAttributeNS(xlinkns, "href", "#discord-def-face-eyes");
          discordEyesSVG.appendChild(useElement);
        } else if (this.discordEyes == "noeyes") {
        }
      });
    },
    wireUpCanvas() {
      if (this.background !== "none") {
        this.canvasID = "canvasID" + this._uid;
        this.$nextTick(function() {
          this.canvas = this.$refs[this.canvasID];
          this.ctx = this.canvas.getContext("2d");
          if (this.RAF !== null) cancelAnimationFrame(this.RAF);
          this.draw();
        });
      }
    },
    draw() {
      let t = this.frames / 200;
      let s,
        i,
        X,
        Z = 0,
        j,
        p,
        w,
        W,
        V;
      let r = q =>
        this.ctx[q ? "lineTo" : "moveTo"](
          w + ((X - 7) / Z) * w * 2,
          (2 / Z) * w
        );
      switch (this.background) {
        case "starfield":
          this.canvas.style.width = "50px";
          this.canvas.style.height = "50px";
          this.canvas.width = 250;
          this.canvas.height = 250;
          this.ctx.fillStyle = "#fffa";
          for (let j = 250, w = 100; j--; ) {
            Z = 1 - (((j * j) / w + this.frames / 100) % 1);
            s = 1 + Math.pow(3 * (1 - Z), 2);
            this.ctx.beginPath();
            this.ctx.arc(
              w + (99 - (j % 199)) / Z,
              100 + (99 - ((j * j * 7) % 198)) / Z,
              s,
              0,
              7
            );
            this.ctx.fill();
          }
          break;
        case "grid":
          this.canvas.style.width = "50px";
          this.canvas.style.height = "50px";
          this.canvas.width = 250;
          this.canvas.height = 250;
          this.ctx.strokeStyle = "#fff4";
          (w = 120), (i = 200), (t = this.frames / 70), (X = 0);
          for (
            ;
            i--;
            this.ctx.lineWidth = 0.5 + 8 / (1 + Z),
              r(Z++),
              r(X--),
              this.ctx.stroke()
          )
            this.ctx.beginPath(),
              (X = (i + Math.sin(t) * 4) % 14),
              (Z = 1 + ((i / 14) | 0) - ((t * 5) % 1)),
              r();
          break;
        case "rush":
          this.canvas.style.width = "50px";
          this.canvas.style.height = "50px";
          this.canvas.width = 250;
          this.canvas.height = 250;
          this.ctx.fillStyle = "#fff3";
          (W = 120), (j = 10), (p = 0), (V = 0), (s = 0);
          for (; --j; )
            for (
              let i = 16;
              i--;
              this.ctx.fillRect(
                W +
                  (Math.sin(
                    (p = 0.39 * i + j / 8 - 6.03 * V - Math.sin(t * 2) * 3)
                  ) /
                    Z) *
                    W -
                  s / 2,
                120 + (Math.cos(p) / Z) * W - s / 2,
                s,
                s
              )
            )
              (Z = 0.5 + j / 2 - t * 6 + (V = (t * 6) | 0)),
                (s = 200 / (1 + Z) / (1 + Z) / (1 + Z));
          break;
      }
      this.frames++;
      this.RAF = requestAnimationFrame(this.draw);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* discrd logo */
.discord-logo {
  transform: scale(0.7);
  transform-origin: 24px 24px;
}

/* ROTATEY animation */
.discord-logo.rotateY-animation .discord-original {
  transition: transform 300ms linear;
  transform-origin: 50% 50%;
}
.discord-logo-container:hover .rotateY-animation .discord-original,
.animated .rotateY-animation .discord-original {
  transform: rotateY(180deg);
}

/* ROTATEX animation */
.discord-logo.rotateX-animation .discord-original {
  transition: transform 300ms linear;
  transform-origin: 50% 50%;
}
.discord-logo-container:hover .rotateX-animation .discord-original,
.animated .rotateX-animation .discord-original {
  transform: rotateX(360deg);
}

/* SHAKE animation */
.discord-logo.shake-animation .discord-original {
}
.discord-logo-container:hover .shake-animation .discord-original,
.animated .shake-animation .discord-original {
  animation-name: shake;
  animation-duration: 100ms;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
}
@keyframes shake {
  2% {
    transform: translate(0.5px, 1.5px) rotate(1.5deg);
  }
  4% {
    transform: translate(0.5px, 1.5px) rotate(1.5deg);
  }
  6% {
    transform: translate(-1.5px, -1.5px) rotate(-0.5deg);
  }
  8% {
    transform: translate(0.5px, -0.5px) rotate(0.5deg);
  }
  10% {
    transform: translate(0.5px, 2.5px) rotate(0.5deg);
  }
  12% {
    transform: translate(2.5px, 1.5px) rotate(-0.5deg);
  }
  14% {
    transform: translate(-1.5px, 2.5px) rotate(-0.5deg);
  }
  16% {
    transform: translate(-0.5px, 0.5px) rotate(0.5deg);
  }
  18% {
    transform: translate(0.5px, 2.5px) rotate(1.5deg);
  }
  20% {
    transform: translate(-0.5px, -0.5px) rotate(0.5deg);
  }
  22% {
    transform: translate(2.5px, 0.5px) rotate(-0.5deg);
  }
  24% {
    transform: translate(-1.5px, -1.5px) rotate(0.5deg);
  }
  26% {
    transform: translate(2.5px, -0.5px) rotate(-0.5deg);
  }
  28% {
    transform: translate(1.5px, -0.5px) rotate(0.5deg);
  }
  30% {
    transform: translate(0.5px, 0.5px) rotate(-0.5deg);
  }
  32% {
    transform: translate(-1.5px, 0.5px) rotate(-0.5deg);
  }
  34% {
    transform: translate(0.5px, 2.5px) rotate(-0.5deg);
  }
  36% {
    transform: translate(-0.5px, -0.5px) rotate(1.5deg);
  }
  38% {
    transform: translate(-1.5px, -1.5px) rotate(0.5deg);
  }
  40% {
    transform: translate(-1.5px, 1.5px) rotate(1.5deg);
  }
  42% {
    transform: translate(0.5px, -1.5px) rotate(1.5deg);
  }
  44% {
    transform: translate(0.5px, 0.5px) rotate(0.5deg);
  }
  46% {
    transform: translate(-1.5px, -1.5px) rotate(1.5deg);
  }
  48% {
    transform: translate(0.5px, -1.5px) rotate(0.5deg);
  }
  50% {
    transform: translate(2.5px, 0.5px) rotate(-0.5deg);
  }
  52% {
    transform: translate(-0.5px, 2.5px) rotate(-0.5deg);
  }
  54% {
    transform: translate(0.5px, 0.5px) rotate(0.5deg);
  }
  56% {
    transform: translate(-1.5px, 2.5px) rotate(0.5deg);
  }
  58% {
    transform: translate(2.5px, 0.5px) rotate(0.5deg);
  }
  60% {
    transform: translate(-1.5px, 2.5px) rotate(0.5deg);
  }
  62% {
    transform: translate(1.5px, -0.5px) rotate(-0.5deg);
  }
  64% {
    transform: translate(1.5px, -1.5px) rotate(1.5deg);
  }
  66% {
    transform: translate(1.5px, -1.5px) rotate(-0.5deg);
  }
  68% {
    transform: translate(0.5px, 2.5px) rotate(-0.5deg);
  }
  70% {
    transform: translate(1.5px, -1.5px) rotate(1.5deg);
  }
  72% {
    transform: translate(1.5px, 1.5px) rotate(-0.5deg);
  }
  74% {
    transform: translate(-0.5px, 1.5px) rotate(1.5deg);
  }
  76% {
    transform: translate(1.5px, 2.5px) rotate(0.5deg);
  }
  78% {
    transform: translate(-0.5px, 0.5px) rotate(0.5deg);
  }
  80% {
    transform: translate(-1.5px, 2.5px) rotate(0.5deg);
  }
  82% {
    transform: translate(0.5px, 2.5px) rotate(-0.5deg);
  }
  84% {
    transform: translate(2.5px, -0.5px) rotate(0.5deg);
  }
  86% {
    transform: translate(1.5px, 0.5px) rotate(0.5deg);
  }
  88% {
    transform: translate(-0.5px, -1.5px) rotate(-0.5deg);
  }
  90% {
    transform: translate(1.5px, -0.5px) rotate(1.5deg);
  }
  92% {
    transform: translate(0.5px, 2.5px) rotate(0.5deg);
  }
  94% {
    transform: translate(2.5px, 0.5px) rotate(-0.5deg);
  }
  96% {
    transform: translate(0.5px, 2.5px) rotate(0.5deg);
  }
  98% {
    transform: translate(2.5px, -1.5px) rotate(1.5deg);
  }
  0%,
  100% {
    transform: translate(0, 0) rotate(0);
  }
}

/* SOFTSHAKE animation */
.discord-logo.softshake-animation .discord-original {
  transform-origin: 24px 24px;
}
.discord-logo-container:hover .softshake-animation .discord-original,
.animated .softshake-animation .discord-original {
  animation: softshake 2000ms linear forwards;
}
@keyframes softshake {
  0%,
  66%,
  100% {
    transform: rotate(0deg);
  }
  3% {
    transform: rotate(-18deg);
  }
  6% {
    transform: rotate(14.4deg);
  }
  9% {
    transform: rotate(-11.5deg);
  }
  12% {
    transform: rotate(9.21deg);
  }
  15% {
    transform: rotate(-7.37deg);
  }
  18% {
    transform: rotate(5.89deg);
  }
  21% {
    transform: rotate(-4.71deg);
  }
  24% {
    transform: rotate(3.77deg);
  }
  27% {
    transform: rotate(-3.02deg);
  }
  30% {
    transform: rotate(2.41deg);
  }
  33% {
    transform: rotate(-1.93deg);
  }
  36% {
    transform: rotate(1.54deg);
  }
  39% {
    transform: rotate(-1.23deg);
  }
  42% {
    transform: rotate(0.99deg);
  }
  45% {
    transform: rotate(-0.79deg);
  }
  48% {
    transform: rotate(0.63deg);
  }
  51% {
    transform: rotate(-0.5deg);
  }
  54% {
    transform: rotate(0.4deg);
  }
  57% {
    transform: rotate(-0.32deg);
  }
  60% {
    transform: rotate(0.25deg);
  }
  63% {
    transform: rotate(-0.2deg);
  }
}

/* SWIRL animation */
.discord-logo.swirl-animation .discord-outer-layer {
  transition: transform 800ms cubic-bezier(0.7, 1, 0.7, 1);
  transform-origin: 50% 50%;
}
.discord-logo-container:hover .swirl-animation .discord-outer-layer,
.animated .swirl-animation .discord-outer-layer {
  transform: scale(1.5) rotate(360deg);
}
.discord-logo.swirl-animation .discord-middle-layer {
  transition: transform 800ms cubic-bezier(0.5, 1, 0.5, 1);
  transform-origin: 50% 50%;
}
.discord-logo-container:hover .swirl-animation .discord-middle-layer,
.animated .swirl-animation .discord-middle-layer {
  transform: scale(1.4) rotate(360deg);
}
.discord-logo.swirl-animation .discord-inner-layer {
  transition: transform 800ms cubic-bezier(0.3, 1, 0.3, 1);
  transform-origin: 50% 50%;
}
.discord-logo-container:hover .swirl-animation .discord-inner-layer,
.animated .swirl-animation .discord-inner-layer {
  transform: scale(1.3) rotate(360deg);
}
.discord-logo.swirl-animation .discord-original {
  transition: visibility 0ms;
  transition-delay: 800ms;
}
.discord-logo-container:hover .swirl-animation .discord-original,
.animated .swirl-animation .discord-original {
  visibility: hidden;
  transition-delay: 0ms;
}
foreignObject {
  width: 100%;
  height: 100%;
}
</style>
