<template>
  <Layout>
    <content-header
      title="Search Collections"
      image="https://iiif.digitalcommonwealth.org/iiif/2/commonwealth:z603vs09b/208,2689,3619,1585/,1200/0/default.jpg"
      :staticImage="false"
    >
    </content-header>

    <div class="container mx-auto px-6">
      <div class="flex flex-wrap my-4">
        <p class="text-3xl font-serif my-10">
          Search <strong>2190</strong> maps, charts, views, plans, and
          manuscripts from <strong>12</strong> institutions.
        </p>

        <div class="flex mx-auto w-full">
          <div class="flex-1">
            <input
              type="text"
              @keyup.enter="enterSearch"
              class="rounded w-full text-3xl font-serif"
              placeholder="Enter names, places, subjects, dates, keywords ..."
              v-model="searchInput"
            />
          </div>
          <div class="pl-3">
            <button
              @click="enterSearch"
              class="rounded-md font-serif text-3xl text-white bg-blue-500 ring-2 py-1 px-3"
            >
              Search
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container mx-auto px-6 mb-10" v-if="state === 'searchReturned'">
           <div id="search-results" class="grid grid-flow-row grid-cols-3 gap-4">


       <div v-for="result in results" v-bind:key="result.id" class="post-card border-gray-200 dark:border-gray-900 bg-white dark:bg-black rounded-lg hover:shadow-xl z-100 dark:bg-gray-900 cursor-pointer p-5" @click="goToResult(result.id)">
                <img v-bind:src="result.image" alt="Placeholder image">
          <p class="text-lg font-serif">{{ result.title }}</p>
          <p class="text-sm text-gray-400">{{ result.collection }}</p>
        </div>

          <div class="post-card border-gray-200 dark:border-gray-900 bg-white dark:bg-black rounded-lg hover:shadow-xl z-100 dark:bg-gray-900 cursor-pointer p-5"><p class="font-serif text-2xl">See all {{ totalResults }} results ❯</p></div>
     </div>
    </div>
  </Layout>
</template>

<script>
import ContentHeader from "~/components/Partials/ContentHeader.vue";

export default {
  components: {
    ContentHeader,
  },

  data: function() {
      return {
        state: 'initial',
        searchInput: '',
        results: [],
        totalResults: 0,
        searchString: ''
      }
    },

  methods: {
    enterSearch: function () {
      this.state = "searching";
      this.results = [];
      this.searchString = `https://collections.leventhalmap.org/search.json?f%5Bcollection_name_ssim%5D%5B%5D=American+Revolutionary+War-Era+Maps+%28Collection+of+Distinction%29&per_page=5&q=${this.searchInput}`;

      fetch(this.searchString)
        .then((response) => response.json())
        .then((result) => {
          this.state = "searchReturned";
          this.totalResults = result.response.pages.total_count;
          result.response.docs.forEach((r) => {
            this.results.push({
              title: r.title_info_primary_tsi,
              date: r.date_start_tsim[0],
              id: r.id,
              collection: r.collection_name_tsim[0],
              image: `https://fedora.digitalcommonwealth.org/fedora/objects/${r.exemplary_image_ssi}/datastreams/thumbnail300/content`,
              url: `https://collections.leventhalmap.org/search/${r.id}`,
            });
          });
        })
        .catch();
    },

    goToResult: function (id) {
      window.location = `/collections/${id}`;
    }
  },

};
</script>