<template>
  <div>
    <b-form>
      <b-form-group
        label="Nr rejestracyjny"
        label-for="input-0"
      >
        <b-form-input
          id="input-0"
          v-model="formData.registration_number"
          type="text"
          required
          placeholder="Nr rejestracyjny"
        ></b-form-input>
      </b-form-group>
      <b-form-group
        label="Marka"
        label-for="input-1"
      >
        <b-form-input
          id="input-1"
          v-model="formData.brand"
          type="text"
          required
          placeholder="Marka"
        ></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-2" label="Model" label-for="input-2">
        <b-form-input
          id="input-2"
          v-model="formData.model"
          required
          placeholder="Model"
        ></b-form-input>
      </b-form-group>
    </b-form>
  </div>
</template>

<script>
export default {
  name: 'VehicleForm',
  props: ['formData'],
}
</script>
