<template>
  <div
    class="base-sticky-container"
    :class="{ shown: closed, 'with-button': hasCTAButton }"
  >
    <div class="grid-container">
      <button @click.prevent="toggle" class="sticky-btn">
        <span class="value">{{ value }}</span>
        <i v-if="!closed" class="far fa-angle-down"></i>
        <i v-else class="far fa-angle-up"></i>
      </button>
      <ul class="sticky-nav" :class="{ shown: closed }">
        <li v-for="(a, i) in hrefs" :key="i">
          <a
            @click.prevent="select"
            :class="a.href"
            :data-key="i"
            :data-id="'#' + a.href"
          >
            {{ a.value }}
          </a>
        </li>
        <li v-if="recruiter" class="recruiter-link">
          <a :href="`mailto:${$t('contact.email')}`">
            <i class="fal fa-envelope"></i>
            <span>Contact {{ recruiter }}</span>
          </a>
        </li>
        <li class="btn-container">
          <button class="btn-primary">Reques Qutoe Button</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  components: {},

  props: {
    hrefs: {
      type: Array,
      required: false,
      default: () => {
        return [];
      }
    },

    hasCTAButton: {
      type: Boolean,
      required: false,
      default: false
    },

    recruiter: { String }
  },
  data() {
    return {
      closed: false,
      selected: false,
      value: null
    };
  },

  mounted() {
    this.value = this.hrefs[0].value;
    window.addEventListener("scroll", this.updateSticky);
    window.addEventListener("scroll", this.updateAnchor);
    this.$el.querySelector(".sticky-nav li a").classList.add("spy");
  },

  methods: {
    toggle() {
      this.closed = !this.closed;
    },

    select(e) {
      const yOffset = -40;
      const a = e.target.closest("a");
      const id = a.getAttribute("data-id");
      const key = Number(a.getAttribute("data-key"));
      const element = document.querySelector(id);

      const y =
        element.getBoundingClientRect().top + window.pageYOffset + yOffset;

      window.scrollTo({ top: y, behavior: "smooth" });

      this.value = this.hrefs[key].value;
      this.closed = false;
    },

    updateSticky() {
      const sticky = document.querySelector(".sticky-container");
      const top = sticky.getBoundingClientRect().top;
      if (top === 0) {
        if (!sticky.classList.contains("top")) {
          sticky.classList.add("top");
        }
      } else {
        sticky.classList.remove("top");
      }
    },

    updateAnchor() {
      const scrollTop =
        document.documentElement.scrollTop || document.body.scrollTop;

      for (let i = 0; i < this.hrefs.length; i++) {
        const id = this.hrefs[i].href;
        const section = document.querySelector(`#${id}`);

        const y = section.offsetTop - 65;

        if (y <= scrollTop) {
          document.querySelector(".spy").classList.remove("spy");
          const spy = document.querySelector(`.${id}`);
          spy.classList.add("spy");
          this.value = this.hrefs[i].value;
        }
      }
    }
  },

  beforeDestroy() {
    window.removeEventListener("scroll", this.updateSticky);
    window.removeEventListener("scroll", this.updateAnchor);
  }
};
</script>
