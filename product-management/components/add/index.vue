<template>
  <div class="modal" v-if="show">
    <div class="modal-content">
      <div class="modal-header">
        <h2 class="modal-title">Add products information</h2>
      </div>
      <form @submit.prevent="submitForm" class="form">
        <div class="form-group">
          <label class="form-label" for="name">Product Name</label>
          <input required class="form-input" type="text" id="name" v-model="name" />
        </div>
        <div class="form-group">
          <label class="form-label" for="description">Description</label>
          <input required class="form-input" type="text" id="description" v-model="description" />
        </div>
        <div class="form-group">
          <label class="form-label" for="price">Price</label>
          <input required class="form-input" type="number" id="Price" v-model="price" />
        </div>
        <div class="form-group">
          <label class="form-label" for="number">Number</label>
          <input required class="form-input" type="number" id="number" v-model="number" />
        </div>
        <div class="form-group">
          <div class="button-group">
            <button type="button" class="cancel-button" @click="onClose">Cancel</button>
            <button type="submit" class="save-button">Add</button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>
      
<script lang="js">

export default {
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    title: {
      type: String,
      default: '',
    },
    onClose: {
      type: Function,
      default: () => { },
    },
  },
  data() {
    return {
      name: "",
      description: "",
      price: 0,
      number: 0,
      available: false,
    };
  },
  methods: {
    closeModal() {
      this.onClose();
    },
    async submitForm() {
      var formData = {
        name: this.name,
        price:parseInt(this.price),
        quantity:parseInt(this.number),
        description: this.description,
        available: this.available,
      };

      await fetch(`http://localhost:8080/products`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(formData),
      })
        .then(response => {
          // Handle the response from the server
          this.$router.go();
          this.show = false;
        })
        .catch(error => {
          // Handle any errors that occurred during the request
          console.error(error);
        });

      this.closeModal();
    },
  },
};
</script>
<style>
.modal {
  position: fixed;
  inset: 0;
  z-index: 50;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: #ffffff;
  width: 33.33%;
  border-radius: 0.5rem;
}

.modal-header {
  padding: 1rem;
}

.modal-title {
  font-size: 1.125rem;
  font-weight: bold;
  margin-bottom: 1rem;
}

.form {
  padding: 1rem;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  align-items: center;
}

.form-group {
  margin-bottom: 1rem;
}

.form-label {
  display: block;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.form-input {
  width: 100%;
  padding: 1rem;
  border: 1px solid #cccccc;
  border-radius: 0.25rem;
  background-color: #ffffff;
}

.button-group {
  width: 100%;
  display: flex;
  justify-content: space-evenly;
}

.cancel-button,
.save-button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 0.25rem;
  color: #ffffff;
  cursor: pointer;
}

.cancel-button {
  background-color: rgb(124, 124, 125);
}

.save-button {
  background-color: rgb(48, 81, 227);
}
</style>