<template>
  <div class="accordion" role="tablist">
    <b-card no-body class="mb-1">
      <b-card-header v-b-toggle.food header-tag="header" class="p-2 bg-primary" role="tab">
        <b-container fluid class="bg-primary">
          <b-row align-v="center" align-h="between">
            <b-col cols="4"><span class="font-weight-bold text-white">Food</span></b-col>
            <b-col cols="1" class="mx-3">
              <router-link :to="'/owner/pet/'+ $route.params.id +'/food'">
              <b-button size="sm"  variant="info">Consult</b-button>
              </router-link>
              </b-col>
          </b-row>
        </b-container>
      </b-card-header>
      <b-collapse id="food" accordion="food" role="tabpanel">
        <b-card-body>
          <b-row>
              <b-col cols="12" class="icon-cards-row" v-if="pet.nextFood">
                    <icon-card :title="$t('Next Food')" icon="iconsminds-clock" :value="moment(pet.nextFood.date).format('DD/MM/YYYY HH:mm')" />
              </b-col>
              <b-col cols="12" class="icon-cards-row" v-else>
                    <icon-card :title="$t('')" icon="iconsminds-close" :value="'There is no food scheduled'" />
              </b-col>
          </b-row>
        </b-card-body>
      </b-collapse>
    </b-card>
  </div>
</template>

<script>

import IconCard from '@/components/Cards/IconCard'
import moment from 'moment'

  export default {
      props: {
         pet: Object
      },
      components: {
         IconCard 
      },
      methods: {
        moment,
      },
    data() {
      return {
        text: `
          Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry
          richardson ad squid. 3 wolf moon officia aute, non cupidatat skateboard dolor
          brunch. Food truck quinoa nesciunt laborum eiusmod. Brunch 3 wolf moon
          tempor, sunt aliqua put a bird on it squid single-origin coffee nulla
          assumenda shoreditch et. Nihil anim keffiyeh helvetica, craft beer labore
          wes anderson cred nesciunt sapiente ea proident. Ad vegan excepteur butcher
          vice lomo. Leggings occaecat craft beer farm-to-table, raw denim aesthetic
          synth nesciunt you probably haven't heard of them accusamus labore VHS.
        `
      }
    }
  }
</script>