<template>
  <div>
    <div>
      <b-button variant="success" v-b-modal.modal-1>Dodaj Pojazd</b-button>
    </div>
    <b-table
      striped
      hover
      :items="items"
      :fields="fields"
      :busy="isLoading"
    >
      <template v-slot:cell(actions)="data">
        <b-button
          size="sm"
          v-on:click="() => {showEditModal(data.item.id)}"
        >
          <font-awesome-icon icon="edit" />
        </b-button>
        <b-button
          variant="danger"
          size="sm"
          v-on:click="() => {deleteVehicle(data.item.id)}"
        >
          <font-awesome-icon icon="trash"  />
        </b-button>
      </template>

    </b-table>
    <b-modal
      id="modal-1"
      :title="modalTitle()"
      @ok="okModal"
      @close="closeModal()"
    >
      <VehicleForm
        :formData="formData"
      />
    </b-modal>
  </div>
</template>

<script>
import axios from 'axios';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faEdit, faTrash } from '@fortawesome/free-solid-svg-icons'

const API = 'http://framelogic/api/';

import VehicleForm from './VehicleForm.vue';

library.add(faEdit, faTrash);

export default {
  name: 'Vehicle',
  components: {
    VehicleForm,
  },
  data() {
    return {
      idToEdit: '',
      formData: {},
      isLoading: false,
      fields: [
        {
          key: 'lp',
          label: 'Lp',
          sortable: true,
        },
        {
          key: 'registration_number',
          label: 'Numer rejestracyjny',
          sortable: true,
        },
        {
          key: 'brand',
          label: 'Marka',
          sortable: true,
        },
        {
          key: 'model',
          label: 'Model',
          sortable: true,
        },
        {
          key: 'created_at',
          label: 'Utworzono',
          sortable: true,
        },
        {
          key: 'last_update',
          label: 'Ostatnia modyfikacja',
          sortable: true,
        },
        {
          key: 'actions',
          label: 'Akcje',
        },
      ],
      items: [],
    }
  },
  mounted () {
    this.isLoading = true;
    axios
      .get(`${API}vehicle/all`)
      .then(response => {
        this.isLoading = false;
        response.data.map((item, index) => {
          this.items.push({lp: (+index+1), ...item});
        });
      })
      .catch(error => {
        this.isLoading = false;
        console.log(error);
      });
  },
  methods: {
    okModal() {
      if (this.idToEdit) {
        return this.updateVehicle();
      }
      return this.createVehicle();
    },
    createVehicle() {
      this.isLoading = true;
      const { registration_number, brand, model } = this.formData;
      const sendData = {
        registration_number,
        brand,
        model,
      };

      axios
        .post(`${API}vehicle/create`, sendData)
        .then(response => {
          const lp = this.items.length + 1;
          this.isLoading = false;
          this.items.push({lp, ...response.data});
        })
        .catch(error => {
          this.isLoading = false;
          console.log(error);
        });
    },
    updateVehicle() {
      this.isLoading = true;
      const { registration_number, brand, model } = this.formData;
      const sendData = {
        registration_number,
        brand,
        model,
      };

      axios
        .put(`${API}vehicle/update?id=${this.idToEdit}`, sendData)
        .then(response => {
          this.isLoading = false;
          const oldItems = [...this.items];
          this.items = [];
          oldItems.map((item, index) => {
            const lp = index + 1;
            if(item.id === this.idToEdit) {
              this.items.push({lp, ...response.data});
            } else {
              this.items.push({lp, ...item});
            }
          });
          this.idToEdit = null;
        })
        .catch(error => {
          this.idToEdit = null;
          this.isLoading = false;
          console.log(error);
        });
    },
    deleteVehicle(id) {
      this.isLoading = true;
      axios
        .delete(`${API}vehicle/delete?id=${id}`)
        .then(() => {
          this.isLoading = false;
          this.items = this.items.filter(item => {
            return item.id !== id;
          });
        })
        .catch(error => {
          this.isLoading = false;
          console.log(error);
        });
    },
    showEditModal(id) {
      console.log(id);

      const items = this.items.filter(item => {
        return item.id === id;
      });

      if (items.length) {
        this.formData = {
          registration_number: items[0].registration_number,
          brand: items[0].brand,
          model: items[0].model,
        }
      }

      this.idToEdit = id;
      this.$bvModal.show('modal-1');

    },
    modalTitle(){
      if (this.idToEdit) {
        return 'Edytuj edytuj';
      }
      return 'Dodaj pojazd'
    },
    closeModal(){
      this.idToEdit = null;
    }
  }
}
</script>
