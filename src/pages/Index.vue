<template>
  <div>
    <q-drawer
      show-if-above
      :width="400"
      :breakpoint="500"
      bordered
    >
        <q-list>
          <div style="display: grid; padding: 50px">
          <q-card>
            <q-card-section>
            <div>
              <q-select
                filled
                v-model="searchCountry"
                use-input
                input-debounce="0"
                label="Поиск по стране"
                :options="countries"
                @filter="filterFn"
                behavior="menu"
              >
                <template v-slot:no-option>
                  <q-item>
                    <q-item-section class="text-grey">
                      Не найдено
                    </q-item-section>
                  </q-item>
                </template>
              </q-select>
            </div>

            <div style="padding-top: 50px">
              <q-select
                filled
                v-model="selectType"
                multiple
                :options="types"
                label="Тип"
              />
            </div>

            <div style="padding-top: 50px" class="text-center">
              <q-checkbox style="margin: 5px; width: 7em" v-for="rat in rating" v-model="ratingSelect" :val=rat.value :label=rat.label />
            </div>

            <div style="padding-top: 50px">
              <q-input
                v-model.number="reviews"
                type="number"
                label="Количество отзывов (от)"
                filled
              />
            </div>

            <div style="padding-top: 50px" class="text-center">
              <span>
                Выберите минимальную стоимость
              </span>
              <q-slider
                name="speed"
                v-model="amountSelect"
                label-always
                :min="0"
                :max=amountMax
                :step="10"
                switch-label-side
                style="padding-left: 10px; padding-right: 10px"
              />
            </div>
            </q-card-section>
            <q-card-actions style="padding-top: 30px">
              <q-btn class="full-width" @click="clearFilters" color="primary" label="Очистить фильтры"/>
            </q-card-actions>

          </q-card>
          </div>
        </q-list>
    </q-drawer>
    <q-page class="flex flex-center">
      <div v-if="filteredAll.length === 0">
        Записей не найдено
      </div>
      <div v-else style="min-width: 100%">
      <q-list bordered separator>
        <q-item v-for="hotel in filteredAll">
          <q-item-section>
              <q-item-label>{{hotel.name}}</q-item-label>
              <q-item-section top>
                <q-item-label caption lines="1">
                  <div class="row">
                    <div class="text-orange" >
                      <q-rating
                        v-model="hotel.rating"
                        max="5"
                        size="1em"
                        color="orange"
                        icon="star_border"
                        icon-selected="star"
                        icon-half="star_half"
                        readonly
                      />
                    </div>
                    <div style="padding-left: 10px">{{hotel.type}}</div>
                    <div style="padding-left: 10px">
                      <q-icon name="reviews"></q-icon>
                      <span>{{hotel.reviews_amount}}</span>
                    </div>
                    <div style="padding-left: 10px">
                      <q-icon name="location_on"></q-icon>
                      <span>{{hotel.country}}</span>
                    </div>
                  </div>
                </q-item-label>
                <hr>
                <div style="max-width: 700px; padding-left: 5px">
                  <q-item-label caption lines="2">{{hotel.description}}</q-item-label>
                </div>
              </q-item-section>

            <q-item-label style="padding-top: 0px; padding-left: 0px">
              <q-chip square  size="11px" v-for="service in hotel.services">
                {{service}}
              </q-chip>
            </q-item-label>


          </q-item-section>
          <q-item-section side style="padding-right: 20px">
            <q-item-label class="text-subtitle1" style="font-size: 1em;">{{hotel.min_price}} рублей</q-item-label>
            <q-item-label caption style="padding-bottom: 10px">Цена за 1 ночь</q-item-label>
            <q-btn color="primary">
              <q-icon left size="1em" name="shopping_cart" />
              <div>Забронировать</div>
            </q-btn>
          </q-item-section>
        </q-item>
      </q-list>
      </div>
<!--      <q-pagination-->
<!--        v-model="page"-->
<!--        :max=totalPage-->
<!--        @click="changePage()"-->
<!--      />-->
    </q-page>
    </div>
</template>

<script>
import { defineComponent } from 'vue';
import { ref } from 'vue'



export default defineComponent({
  name: 'PageIndex',
  data: () => ({
    hotels: [
      {
        name: "Marina Inn",
        country: "Греция",
        address: "Фалираки, Родос, Греция",
        stars: 4,
        type: "Отель",
        description: "Этот 4-звездочный отель расположен в центре города. На его территории есть бассейн с террасой и сауна. Из номеров открывается вид на море или на средневековый город.",
        services: ["Пляж","Кондиционер","Открытый бассейн","Бесплатная парковка","Бесплатный WiFi","Вид на море","Бесплатный завтрак"],
        min_price: 2789.00,
        currency: "RUR",
        rating: 4,
        reviews_amount: 18,
        last_review: "Отличное расположение. Вкусный завтрак. Отдыхали с детьми - все понравилось."
      },
      {
        name: "Mondrian Suites",
        country: "Греция",
        address: "Фалираки, Родос, Греция",
        stars: 5,
        type: "Апартаменты",
        description: "Из окон открывается вид на город.К услугам гостей номера-студио с балконом и чайником в 2,7 км от пляжа.",
        services: ["Пляж","Кондиционер","Открытый бассейн","Платная парковка","Бесплатный WiFi","Вид на море"],
        min_price: 3245.20,
        currency: "RUR",
        rating: 5,
        reviews_amount: 4,
        last_review: "Потрясающее место, в номере есть все необходимое. Красивый вид на море."
      },
      {
        name: "Sunny Appartments",
        country: "Греция",
        address: "Родос, Родос, Греция",
        stars: 2,
        type: "Апартаменты",
        description: "Все номера и апартаменты оборудованы кондиционером и телевизором с плоским экраном. Также в распоряжении гостей тостер и чайник.",
        services: ["Пляж","Кондиционер","Бесплатная парковка","Бесплатный WiFi"],
        min_price: 1153.00,
        currency: "RUR",
        rating: 2.4,
        reviews_amount: 36,
        last_review: "Бассейн очень маленький. Кормят невкусно. Больше не поедем."
      },
      {
        name: "Super All Inclusive Hotel",
        country: "Греция",
        address: "Родос, Родос, Греция",
        stars: 4,
        type: "Отель",
        description: "Все номера оснащены телевизором с плоским экраном. Из некоторых номеров открывается вид на море или город.",
        services: ["Пляж","Кондиционер","Открытый бассейн","Бесплатный WiFi","Вид на море","Бесплатный завтрак"],
        min_price: 3689.00,
        currency: "RUR",
        rating: 4.1,
        reviews_amount: 14,
        last_review: "Недалёко от пляжа и старого города, вокруг много разных магазинчиков"
      },
      {
        name: "Adams Hotel",
        country: "Греция",
        address: "Родос, Родос, Греция",
        stars: 3,
        type: "Отель",
        description: "Отель расположен всего в 100 метрах от пляжа и в 5-ти минутах ходьбы от исторической части города, недалеко от всех основных достопримечательностей. Из отеля открывается вид на море. К услугам гостей спокойный открытый бассейн.",
        services: ["Пляж","Кондиционер","Открытый бассейн","Бесплатная парковка","Бесплатный WiFi","Бесплатный завтрак"],
        min_price: 1896.00,
        currency: "RUR",
        rating: 0,
        reviews_amount: 0,
        last_review: ""
      },
      {
        name: "У Тёти Марины",
        country: "Россия",
        address: "Пушкина 14",
        stars: 1,
        type: "Отель",
        description: "Этот 1-звездочный отель расположен в центре города. Из номеров открывается вид на огород.",
        services: ["Бесплатная парковка"],
        min_price: 980,
        currency: "RUR",
        rating: 1,
        reviews_amount: 1,
        last_review: "Отличное расположение. Вкусный завтрак. Отдыхали с детьми - все понравилось."
      },
      {
        name: "Ночничок",
        country: "Россия",
        address: "Ленина 187",
        stars: 3,
        type: "Отель",
        description: "Все номера оснащены телевизором с плоским экраном. Из некоторых номеров закрывается вид на город. Также в распоряжении гостей тостер и чайник.",
        services: ["Бесплатная парковка","Бесплатный WiFi"],
        min_price: 1350,
        currency: "RUR",
        rating: 3,
        reviews_amount: 4,
        last_review: "Отличное расположение. Вкусный завтрак. Отдыхали с детьми - все понравилось."
      },
    ],
    countries: [],
    origCountries: [],
    searchCountry: null,
    types: [],
    selectType: [],
    ratingSelect: ref([0,1,2,3,4,5]),
    rating: [
      {
        label: '0 звезд',
        value: 0,
      },
      {
        label: '1 звезда',
        value: 1,
      },
      {
        label: '2 звезды',
        value: 2,
      },
      {
        label: '3 звезды',
        value: 3,
      },
      {
        label: '4 звезды',
        value: 4,
      },
      {
        label: '5 звезд',
        value: 5,
      },
    ],
    reviews: null,
    amountSelect: null,
    amountMax: null,
    page: 1,
    limit: 3,
    totalPage: 0,
    filter: false,


  }),
  methods: {
    filterFn(val, update) {
      if (val === '') {
        update(() => {
          this.countries = this.origCountries
        })
        return
      }
      update(() => {
        const needle = val.toLowerCase()
        this.countries = this.origCountries.filter(v => v.toLowerCase().indexOf(needle) > -1)
      })
    },
    clearFilters(){
        this.searchCountry = null
        this.selectType = []
        this.ratingSelect = ref([0,1,2,3,4,5])
        this.reviews = null
        this.amountSelect = null
    },
    changePage(){

    }
  },
  computed: {
    pageData(){
      return this.hotels.slice(this.page * this.limit - this.limit, this.page * this.limit)
    },
    filteredHotels(){
      if(this.searchCountry === null) return this.hotels
        else return this.hotels.filter(hotel => hotel.country.includes(this.searchCountry))
      },
    filteredTypes(){
        if(this.selectType.length === 0) return this.hotels
          return this.hotels.filter(hotel => this.selectType.includes(hotel.type))
    },
    filteredRating(){
      if(this.ratingSelect === null) return this.hotels
        return this.hotels.filter(hotel => this.ratingSelect.includes(Math.floor(hotel.rating)))
    },
    filteredReviews(){
      if(this.reviews === null) return this.hotels
        return this.hotels.filter(hotel => hotel.reviews_amount >= this.reviews )
    },
    filteredAmount(){
      if(this.amountSelect === null) return this.hotels
        return this.hotels.filter(hotel => hotel.min_price <= this.amountSelect )
    },
    filteredAll() {
      return this.filteredTypes
        .filter(hotel => this.filteredHotels.includes(hotel))
        .filter(hotel => this.filteredRating.includes(hotel))
        .filter(hotel => this.filteredReviews.includes(hotel))
        .filter(hotel => this.filteredAmount.includes(hotel))
    }
  },
  mounted() {
    this.hotels.forEach(el => {
      if(!this.origCountries.includes(el.country)) {this.countries.push(el.country); this.origCountries.push(el.country)}
      if(!this.types.includes(el.type)) this.types.push(el.type);
      if(el.min_price >= this.amountMax) {this.amountMax = el.min_price; this.amountSelect = this.amountMax}
    })
    this.totalPage = Math.ceil(this.hotels.length / this.limit);
  }
})
</script>
