<script>
import { properties as data } from "./data/properties";
import HeaderBar from "./components/HeaderBar.vue";
import PropertyCard from "./components/PropertyCard.vue";
import SearchSortBar from "./components/SearchSortBar.vue";

export default {
  components: { HeaderBar, PropertyCard, SearchSortBar },

  data() {
    return {
      properties: data.map(p => ({ ...p, bookmarked: false })),
      searchText: "",
      sortBy: "low",
    };
  },

  computed: {
    filteredProperties() {
      const term = this.searchText.toLowerCase();

      let results = this.properties.filter(
        p =>
          p.title.toLowerCase().includes(term) ||
          p.location.toLowerCase().includes(term)
      );

      results.sort((a, b) =>
        this.sortBy === "low" ? a.price - b.price : b.price - a.price
      );

      return results;
    }
  },

  methods: {
    toggleBookmark(id) {
      const property = this.properties.find(p => p.id === id);
      property.bookmarked = !property.bookmarked;
      localStorage.setItem("bookmarks", JSON.stringify(this.properties));
    }
  },

  mounted() {
    const saved = JSON.parse(localStorage.getItem("bookmarks"));
    if (saved) this.properties = saved;
  }
};
</script>

<template>
  <div>
    <HeaderBar :total="filteredProperties.length" />

    <SearchSortBar
      :search-value="searchText"
      :sort-value="sortBy"
      @update:search="searchText = $event"
      @update:sort="sortBy = $event"
    />

    <div class="grid">
      <PropertyCard
        v-for="p in filteredProperties"
        :key="p.id"
        :property="p"
        @toggle-bookmark="toggleBookmark"
      />
    </div>
  </div>
</template>

<style>
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 20px;
  padding: 20px;
}
</style>
