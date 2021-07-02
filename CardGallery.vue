<template>
  <b-row no-gutters class="card-gallery">
    <!-- MODAL START -->
    <b-modal ref="templatePreview">
      <b-carousel v-if="toPreview" controls indicators>
        <b-carousel-slide
          v-for="variant in toPreview.variant_templates"
          :key="variant.id"
          :img-src="variant.front_image"
        ></b-carousel-slide>
      </b-carousel>

      <template v-slot:modal-footer="{ cancel }">
        <b>Custom Footer</b>
        <b-button @click="cancel()" size="sm" variant="dark">Close</b-button>
      </template>
    </b-modal>
    <!-- MODAL END -->
    <b-col cols="12" sm="4" md="3" lg="2">
      <filter-sidebar></filter-sidebar>
    </b-col>

    <b-col id="cardGallery" :class="{ 'ml-5': !isXs }" data-spy="scroll" data-target="#filterBtn">
      <b-row style="max-width: 100%">
        <b-col
          v-for="variant in template.displayedTemplates"
          :key="variant.id"
          cols="6"
          sm="6"
          md="4"
          lg="3"
        >
          <card-thumb
            :temp="variant.template"
            :variant="variant"
            @template-preview="templatePreview"
          />
        </b-col>
      </b-row>
      <!-- PAGE CONTROLS -->
      <template v-if="page != 1">
        <nuxt-link :to="{ name: 'cards', query: { page: page - 1 } }" rel="prev">Previous Page</nuxt-link>
      </template>
      <div>
        <b-button @click="query.page += 1" variant="light">next</b-button>
        <nuxt-link :to="{ name: 'cards', query: { page: page + 1 } }" rel="next">Next Page</nuxt-link>
      </div>
      <!-- PAGE CONTROLS END -->
    </b-col>
  </b-row>
</template>

<script>
import { mapState, mapActions, mapGetters } from 'vuex'
import { vueWindowSizeMixin } from 'vue-window-size'
import FilterSidebar from '@/components/FilterSidebar'
import CardThumb from '@/components/CardThumb'

export default {
  name: 'CardGalleryNew',
  components: {
    FilterSidebar,
    CardThumb,
  },
  mixins: [vueWindowSizeMixin],
  props: ['category'],
  data() {
    return {
      toPreview: '',
      loading: false,
    }
  },
  computed: {
    isXs() {
      return this.windowWidth < 576
    },
    page() {
      return parseInt(this.$route.query.page) || 1
    },
    ...mapGetters('template', ['getTemplatesByColor']),
    ...mapState(['template']),
  },
  methods: {
    templatePreview(variant) {
      this.toPreview = variant
      this.$refs.templatePreview.show()
    },
    hideTemplatePreview() {
      this.$refs.templatePreview.hide()
    },
    ...mapActions('template', ['fetchTemplates']),
    ...mapActions('assets', ['fetchAssets']),
  },
}
</script>

<style scoped lang="scss">
#cardGallery {
  background-color: $white;
  padding: 40px 0 0 30px;
  width: 100%;
}

.card-gallery {
  background-color: $creamsoda;
}

.btn-secondary {
  background-color: #ffffff00;
  border: none;
  box-shadow: none;
  -webkit-box-shadow: none;
  :hover {
    background-color: #ffffff00;
    border: none;
    box-shadow: none;
    box-shadow: none;
    -webkit-box-shadow: none;
  }
  :focus {
    background-color: #ffffff00;
    border: none;
    box-shadow: none;
    box-shadow: none;
    -webkit-box-shadow: none;
  }
  &:not(.disabled):active {
    background-color: #ffffff00;
    border: none;
    box-shadow: none;
    -webkit-box-shadow: none;
  }
}

.centered-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.qp-button {
  background-color: $charcoal;
  color: $creamsoda;
  font-family: $font-family-secondary;
  text-transform: uppercase;
  font-size: 0.65em;
  letter-spacing: 0.07em;
  padding: 6px 4px 6px 4px;
  display: none;
}

.gallery-thumb:hover {
  .qp-button {
    display: inherit;
  }
}
.variant_thumb {
  height: 30px;
  width: 30px;
  overflow: hidden;
  display: inline-block;
  border-radius: 50%;
}
</style>
