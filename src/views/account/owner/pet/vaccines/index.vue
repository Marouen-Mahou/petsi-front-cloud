<template>
  <div>
      <b-row class="m-2" align-h="between">
          <b-col cols="1">
                <router-link :to="'/owner/pet/'+$route.params.id+'/all'"
          ><b-button size="sm"
            ><i class="iconsminds-left"/></b-button></router-link
        >
          </b-col>
          <b-col cols="4" >
            <b-button v-if="selected" class="float-right" v-b-modal.modalEditVaccine variant="secondary" size="sm">Edit Vaccine</b-button>
                        <b-modal
                        v-if="selected"
              id="modalEditVaccine"
              ref="modalEditVaccine"
              :title="$t('Vaccines')"
              modal-class="modal-right"
            >
              <b-form>
                <b-form-group :label="$t('name')">
                  <b-form-input v-model="selected.name" />
                </b-form-group>
                <b-form-group :label="$t('veterinary')">
                  <b-form-select
                    v-model="selected.vet"
                    :options="vetOptions"
                  ></b-form-select>
                </b-form-group>
                <b-form-group :label="$t('Date')">
                  <b-form-datepicker
                    id="minDate"
                    v-model="selected.date"
                    size="lg"
                    today-button
                    close-button
                    start-weekday="1"
                    value-as-date
                    locale="en-GB"
                  />
                </b-form-group>
                <b-form-group :label="$t('Description')">
                  <b-form-input v-model="selected.desc" />
                </b-form-group>
              </b-form>

              <template slot="modal-footer">
                <b-button
                  variant="outline-secondary"
                  @click="hideModal('modalEditVaccine')"
                  >{{ $t("pages.cancel") }}</b-button
                >
                <b-button
                  variant="primary"
                  @click="updateVaccine()"
                  class="mr-1"
                  >{{ $t("pages.submit") }}</b-button
                >
              </template>
            </b-modal>
            <b-button class="float-right mr-2" v-b-modal.modalVaccine variant="primary" size="sm">Add Vaccine</b-button>
            <b-modal
              id="modalVaccine"
              ref="modalVaccine"
              :title="$t('Vaccines')"
              modal-class="modal-right"
            >
              <b-form>
                <b-form-group :label="$t('name')">
                  <b-form-input v-model="newVaccine.name" />
                </b-form-group>
                <b-form-group :label="$t('veterinary')">
                  <b-form-select
                    v-model="newVaccine.vet"
                    :options="vetOptions"
                  ></b-form-select>
                </b-form-group>
                <b-form-group :label="$t('Date')">
                  <b-form-datepicker
                    id="minDate"
                    v-model="newVaccine.date"
                    size="lg"
                    today-button
                    close-button
                    start-weekday="1"
                    value-as-date
                    locale="en-GB"
                  />
                </b-form-group>
                <b-form-group :label="$t('Description')">
                  <b-form-input v-model="newVaccine.desc" />
                </b-form-group>
              </b-form>

              <template slot="modal-footer">
                <b-button
                  variant="outline-secondary"
                  @click="hideModal('modalVaccine')"
                  >{{ $t("pages.cancel") }}</b-button
                >
                <b-button
                  variant="primary"
                  @click="addNewVaccine()"
                  class="mr-1"
                  >{{ $t("pages.submit") }}</b-button
                >
              </template>
            </b-modal>
          </b-col>
      </b-row>
    <b-card>
      <b-card-header class="mb-1 font-weight-bold text-primary">
        <h3>{{ $t("Vaccines") }}</h3>
      </b-card-header>
      <b-table
        ref="custom-table"
        class="vuetable"
        sort-by="title"
        sort-desc.sync="false"
        @row-selected="onRowSelected"
        selectable
        :select-mode="bootstrapTable.selectMode"
        :current-page="currentPage"
        selectedVariant="primary"
        :fields="bootstrapTable.fields"
        :items="vaccinesList"
      >
        <template #cell(done)="vaccine">
          <b-button @click="confirmVaccine(vaccine.item._id)" v-if="!vaccine.item.done" variant="success" size="sm">
            Confirmer
          </b-button>
          <b-button @click="deleteVaccine(vaccine.item._id)"  variant="danger" size="sm">
            Delete
          </b-button>
        </template>
      </b-table>
      <b-pagination
        size="sm"
        align="center"
        :total-rows="totalRows"
        :per-page="perPage"
        v-model="currentPage"
      >
        <template v-slot:next-text>
          <i class="simple-icon-arrow-right" />
        </template>
        <template v-slot:prev-text>
          <i class="simple-icon-arrow-left" />
        </template>
        <template v-slot:first-text>
          <i class="simple-icon-control-start" />
        </template>
        <template v-slot:last-text>
          <i class="simple-icon-control-end" />
        </template>
      </b-pagination>
    </b-card>
  </div>
</template>
<script>
import VuetablePaginationBootstrap from "@/components/Common/VuetablePaginationBootstrap";
import moment from 'moment'

export default {
  components: {
    "vuetable-pagination-bootstrap": VuetablePaginationBootstrap
  },
  data() {
    return {
      newVaccine: {
        name: null,
        vet: null,
        date: null,
        desc: null,
      },
      vetOptions: [],
      vaccinesList: [],
      petId: this.$route.params.id,
      currentPage: 1,
      perPage: 5,
      totalRows: 0,
      selected: null,
      bootstrapTable: {
        selected: [],
        selectMode: "single",
        fields: [
          {
            key: "name",
            label: "Name",
            sortable: true,
            sortDirection: "desc",
            tdClass: "list-item-heading"
          },
          {
            key: "vetName",
            label: "Veterinary",
            sortable: true,
            tdClass: "text-muted"
          },
          {
            key: "dateForm",
            label: "Date",
            sortable: true,
            tdClass: "text-muted"
          },
          {
            key: "desc",
            label: "Description",
            sortable: true,
            tdClass: "text-muted"
          },
          { key: "done", label: "Action", tdClass: "text-muted" }
        ]
      }
    };
  },
  mounted(){
     this.getVets();
     this.getVaccines();
  },
  computed: {
    dataProvider(ctx) {
      return this.vaccinesList
    },
  },
  methods: {
     moment,
      hideModal(refname) {
      this.$refs[refname].hide();
    },
    onRowSelected(items) {
        this.selected = items[0]
      },
    addNewVaccine() {
      let v = this.newVaccine

      if(!v.name || !v.vet || !v.date ) {
        this.$notify("error", "Update Pet", "Fill all fields", {
                        duration: 3000,
                        permanent: false
                        });
        return
      }
     

      this.$Axios.post('/pet/'+this.petId+'/vaccine',this.newVaccine)
      .then(res => {
        this.newVaccine = {}
        this.getVaccines()
        this.hideModal('modalVaccine')
        this.$notify("success", "Vaccine", "Vaccine added", {
          duration: 3000,
          permanent: false
        });
      })
      .catch(e => {
        console.log(e)
      })
    },
    onPaginationData(paginationData) {
      this.$refs.pagination.setPaginationData(paginationData);
    },
    onChangePage(page) {
      this.$refs.vuetable.changePage(page);
    },
    getVets(){
      this.$Axios.get('/getUsers')
      .then(res => {
        console.log(res.data)
        this.vetOptions= []
        res.data.forEach(user => {
          if(user.role == 'Veterinary'){
            let option = {
              value: user._id,
              text: user.name + " " + user.surname,
            }
            this.vetOptions.push(option)
          }
        })
      })
      .catch(e => {
        console.log(e)
      })
    },
    getVaccines(){
      this.$Axios.get('/pet/'+this.petId+'/vaccine')
      .then(res => {
        this.vaccinesList= []
        console.log(res.data)
        res.data.forEach(vacc => {
          let vaccine = {
              _id: vacc._id,
              name: vacc.name,
              vet: vacc.vet._id,
              vetName: vacc.vet.name + ' ' + vacc.vet.surname,
              date: vacc.date,
              dateForm: this.moment(vacc.date).format('DD-MM-YYYY'),
              desc: vacc.name,
              done: vacc.done,
          }
          this.vaccinesList.push(vaccine)
        })
      })
      .catch(e => {
        console.log(e)
      })
    },
    confirmVaccine(id){
      if(!confirm('are you sure ?')) return
      this.$Axios.put('/pet/'+this.petId+'/vaccine/' + id,{done: true})
        .then(res => {
          this.getVaccines()
          this.$notify("success", "Vaccine", "Vaccine confimed", {
          duration: 3000,
          permanent: false
        });
        })
        .catch(e => console.log(e))
    },
    updateVaccine(){
      let v = this.selected
      if(!v.name || !v.vet || !v.date ) {
        this.$notify("error", "Update Pet", "Fill all fields", {
                        duration: 3000,
                        permanent: false
                        });
        return
      }

      this.$Axios.put('/pet/'+this.petId+'/vaccine/' + this.selected._id,this.selected)
        .then(res => {
          this.getVaccines()
          
          this.hideModal('modalEditVaccine')
          this.$notify("success", "Vaccine", "Vaccine updated", {
          duration: 3000,
          permanent: false
        });
        })
        .catch(e => console.log(e))
    },
    deleteVaccine(id){
      if(!confirm('are you sure ?')) return
       this.$Axios.delete('/pet/'+this.petId+'/vaccine/' + id)
        .then(res => {
          this.getVaccines()
          this.$notify("success", "Vaccine", "Vaccine deleted", {
          duration: 3000,
          permanent: false
        });
        })
        .catch(e => console.log(e))
    },
  }
};
</script>
