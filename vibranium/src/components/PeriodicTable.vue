<template>
<div>

  <v-layout row wrap style="width:80%;margin:0 auto;">
    <v-flex xs12>
      <div class="button-group">
        <v-btn class='md-raised' v-for="(val, key) in options.getSortData" :class="[key===sortOption? 'is-checked' : '']" @click="$refs.isotope.sort(key)">{{key}}</v-btn>
      </div>
    </v-flex>
    <v-flex xs12>
      <v-slider min='0' max='6000' v-model="currentTemperature"></v-slider>
      {{currentTemperature}} K
    </v-flex>
  </v-layout>
  <v-layout>
    <v-flex xs12>
      <isotope style="margin-top:50px;margin-left:2%" ref="isotope" id="root_isotope1" :list="list" :options='options' @filter="filterOption=arguments[0]" @sort="sortOption=arguments[0]">
        <div v-for="(element,index) in list" :key="index" class="pa3 ma3">
          <router-link :to="'/element/'+String(element.symbol).toLowerCase()" :ripple='true' ripple :hover='true' hover>
            <v-card class="card" style="width:150px" :class="{blue: (currentTemperature< parseFloat(element['melting-point'])), green: (currentTemperature>parseFloat(element['melting-point']) && currentTemperature<parseFloat(element['boiling-point'])) , red: (currentTemperature >= parseFloat(element['boiling-point'])) }">
              <v-card-title>
                {{element.symbol}}
              </v-card-title>
              {{element['atomic-number']}}<br>
              {{element.name}}<br />
              {{element.number}}<br />
              {{element['atomic-weight']}}

              <v-card-actions>
              </v-card-actions>
            </v-card>
          </router-link>
        </div>
      </isotope>
    </v-flex>
  </v-layout>
</div>
</template>

<script>
import data from "./periodic_table.js";
import isotope from 'vueisotope';
import $ from 'jquery';
import _ from 'lodash';

console.log(data);
export default {
  components: {
    isotope
  },
  mounted: function() {
    console.log("Fetching data");
    var _this = this;
    fetch('http://localhost:5000/data/all').then((d) => d.json()).then(x => _this.list = _.values(x))
  },
  data: function() {
    var _this = this;
    return {
      currentTemperature: 10,
      currentLayout: "masonry",
      filterText: '',
      list: [],
      selected: null,
      sortOption: "original-order",
      filterOption: "filterByText",
      options: {
        filter: 'filterByText',
        getFilterData: {
          filterByText: function(itemElem) {
            return itemElem.name.toLowerCase().includes(_this.filterText.toLowerCase());
          }
        },
        getSortData: {
          name: "name",
          symbol: "symbol",
          number: 'atomic-number'
        },
        cellsByRow: {
          columnWidth: 230,
          rowHeight: 230
        },
        masonryHorizontal: {
          rowHeight: 110
        },
        cellsByColumn: {
          columnWidth: 220,
          rowHeight: 220
        },
        packery: {
          gutter: 20
        }
      }
    }
  }
};
</script>

<style>
.col: {
  width: 80%
}

.liquid {
  background-color: green
}

.solid {
  background-color: blue
}

.gas {
  background-color: red
}

.md-card: {
  width: 40px
}
</style>
