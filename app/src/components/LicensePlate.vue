<template>
    <div>
      <div v-if="!hasRecords" style="text-align: center"><br><br>No Records</div>
      <div v-if="hasRecords">
        
        <b-table label-sort-asc="" label-sort-desc="" label-sort-clear="" :items="plates" :fields="column" striped hover :current-page="currentPage" :per-page="perPage">
        </b-table>
        <b-row>
              <b-pagination :total-rows="totalRows" :per-page=10 v-model="currentPage" align="center" />

        </b-row>
      </div>
    </div>
</template>


<script>
import { DateTime } from "luxon";
export default {
  props:{
    plates : Array
  },
  data() {
    return {
      selectAll: false,
      perPage: 10,
      currentPage: 1,
      column: [
        {
          key: "id",
          sortable: true,
          label: "Id",
          
        },
        {
          key: "plate",
          sortable: true,
          label: "License Plate",
        },
        {
          key: "start_date",
          sortable: true,
          label: "Start Date",
          formatter : "formatDateAssigned"
          
        },
        {
          key: "end_date",
          sortable: true,
          label: "End Date",
          formatter : "formatDateAssigned"
        },
        {
          key: "end_date",
          sortable: true,
          label: "End Date",
        },
        {
          key: "owner_name",
          sortable: true,
          label: "Owner Name",
        }

      ]
    };
  },
  computed: {
    hasRecords() {
      return this.plates.length > 0;
    },
    totalRows() {
      return this.plates.length;
    }
  },
  methods: {
    formatDateAssigned(value) {
      if (value){
      const formattedDate = DateTime.fromISO(
        value
      ).toLocaleString(DateTime.DATE_FULL);
      return formattedDate;}
      else return "";
    }
  
  },
  components: {},
  mounted() {
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
