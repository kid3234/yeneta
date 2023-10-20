<template>
  <div>
    <div class=" deldive" v-if="showalert">
      <div class="delsdiv ">
        <h2 class=" delh2">Confirm your action</h2>
        <p>Are you sure?</p>
        <div class="delbtndiv ">
          <button class=" cdelbtn yesbtn " @click="handleYes">Yes</button>
          <button class="no cdelbtn" @click="handleNo">No</button>
        </div>
      </div>
    </div>
    <div class="nav">

      <div class="sfdiv ">
        <label for="name" style="color: white;">Filter:</label>
        <input required class="sinput" placeholder="product name" type="text" name="name" v-model="searchQuery">
      </div>
      <button @click="add" class="addbtn">Add Product</button>
    </div>
    <div class="tdiv">
      <table>
        <thead>
          <tr class="trh">
            <th class="th">Product ID</th>
            <th class="th">Product Name</th>
            <th class="th">Product Description</th>
            <th class="th">Price</th>
            <th class="th">Number</th>
            <th class="th">Status</th>
            <th class="th">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(product, index) in sortedProducts" :key="index" class="trd">
            <td class="td">{{ product.id }}</td>
            <td class="td">{{ product.name }}</td>
            <td class="td">{{ product.description }}</td>
            <td class="td">{{ product.price }}</td>
            <td class="td">{{ product.quantity }}</td>
            <td class="td"><button  :class="{ 'statusbtn'  : product.available, 'notavail': !product.available }"  @click="toglestatus(product)">{{ product.available ? 'available'
              : 'not_available' }}</button></td>
            <td class="td tdbtn">
              <button class="upbtn" @click="updateProduct(product)">Edit</button>
              <button class="delbtn" @click="handledelete(product)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <add :show="showModal" title="Subscribe to our Newsletter" :onClose="closeModal">
    </add>
    <div v-if="modalup">
      <update :show="showModalup" title="Subscribe to our Newsletter" :products="selectedproduct" :onClose="closeModal">
      </update>
    </div>
  </div>
</template>

<script>
import update from '@/components/update'
import add from '@/components/add'

export default {
  components: {
    update,
    add
  },
  data() {
    return {
      selectedproduct: null,
      showModal: false,
      status: false,
      showModalup: false,
      showalert: false,
      modalup: false,
      products: [],
      searchQuery: "",
      available: false,
    };
  },

  computed: {
    sortedProducts() {
      let filteredProducts = this.products;

      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        filteredProducts = this.products.filter(product => {
          // Perform case-insensitive search on relevant product properties
          return (
            product.id.toString().toLowerCase().includes(query) ||
            product.name.toLowerCase().includes(query) ||
            product.description.toLowerCase().includes(query) ||
            product.price.toString().toLowerCase().includes(query) ||
            product.quantity.toString().toLowerCase().includes(query)
          );
        });
      }

      return filteredProducts;
    }
  },
  async mounted() {
    await fetch('http://localhost:8080/products', {
      method: 'GET'
    })
      .then(response => {
        if (response.ok) {
          return response.json(); // Parse the response body as JSON
        } else {
          throw new Error('Request failed with status code ' + response.status);
        }
      })
      .then(data => {
        this.products = data;
        console.log(data); // Access the data here
      })
      .catch(error => {
        console.error(error);
      });


    this.closeModal();
  },
  methods: {
    handleYes() {
      this.deleteCon = true;
      this.showalert = false;
      console.log("User clicked Yes");
      this.deleteMerchant();
    },
    handleNo() {
      this.deleteCon = false;
      this.showalert = false;
      console.log("User clicked No");
    },
    async handledelete(product) {
      this.selectedproduct = product;
      this.showalert = true;
    },
    async deleteMerchant() {
      if (this.deleteCon) {


        await fetch(`http://localhost:8080/products/${this.selectedproduct.id}`, {
          method: 'DELETE'
        })
          .then(response => {
            // Handle the response from the server
            this.$router.go();
            this.products = response.data
          })
          .then(data => {
            this.products = data
            // Handle the parsed response data
            console.log(data); // Access the data here
          })
          .catch(error => {
            // Handle any errors that occurred during the request
            console.error(error);
          });
      }
    },
    async toglestatus(product) {
      const formData = {
        available: !product.available,
      };
      console.log(formData);

      await fetch(`http://localhost:8080/products/${product.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(formData),
      })
        .then(response => {
          if (response.ok) {
            // Request successful
            this.$router.go()
            console.log('Product updated successfully');
          } else {
            // Request failed
            console.error('Failed to update product');
          }
        })
        .catch(error => {
          console.error('An error occurred:', error);
        });
      this.closeModal();

    },
    updateProduct(product) {
      // Handle update logic here
      this.selectedproduct = product
      this.showModalup = true
      this.modalup = true
    },
    add() {
      this.showModal = true
    },

    closeModal() {
      this.showModalup = false
      this.showModal = false
    }

  }
};
</script>

<style>
.td {
  padding: 12px 18px;
}

.th {
  padding: 8px 18px;
}

.trd {
  background-color: white;
  border: 2px solid black;
}

.trh {
  background-color: gray;
}

table {
  width: 75%;
  text-align: left;
  color: black;
  margin: 0 auto;
  margin-top: 30px;
}

.tdiv {
  margin-left: 20px;
  margin-right: 20px;
  margin-top: 12px;
}

.sfdiv {

  color: black;
  margin-left: 20px;
  gap: 10px;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}

.sinput {
  background-color: rgb(250, 247, 247);
  width: 200px;
  height: 32px;
  border: 1px solid gray;
  border-radius: 8px;
  padding: 5px;
}

.upbtn {
  background-color: green;
  color: white;
  width: 100px;
  padding: 10px 5px;
  border-radius: 10px;

}

.notavail {
  background-color: rgb(95, 97, 94);
  color: white;
  width: 100px;
  padding: 10px 5px;
  border-radius: 10px;
}

.statusbtn {
  background-color: rgb(62, 122, 32);
  color: white;
  width: 100px;
  padding: 10px 5px;
  border-radius: 10px;
}

.delbtn {
  background-color: rgb(5, 69, 90);
  color: white;
  width: 100px;
  padding: 10px 5px;
  border-radius: 10px;
}

body {
  background-color: #2F2E41
}

.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgb(22, 23, 48);
  padding: 20px;
}

.addbtn {
  background-color: rgb(22, 30, 87);
  color: white;
  width: 150px;
  padding: 10px 10px;
  border-radius: 10px;
  margin-right: 20px;
}
.tdbtn{
  display: flex;
  justify-content: space-evenly;
}
.deldive {
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  display: flex;
  position: fixed;
  justify-content: center;
  align-items: center;
}

.delsdiv {
  background-color: #1a1e34;
  width: 24rem;
  color: #ffffff;
  border-radius: 0.5rem;
  padding: 1.5rem;
  height: 150px;
}

.delh2 {
  font-size: 1.25rem;
  font-weight: bold;
  margin-bottom: 1rem;
}

.delbtndiv {
  margin-top: 1rem;
  justify-content: space-between;
  position: fixed;
}


.yesbtn {
  background-color: #68bcfb;
}

.yesbtn :hover {
  background-color: #2779bd;
}

.cdelbtn {
  margin-right: 0.5rem;
  padding-left: 1rem;
  padding-right: 1rem;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  color: #ffffff;
  border-radius: 0.25rem;
}

.no {
  background-color: #cbd5e0;
}

.no:hover {
  background-color: #a0aec0;
}</style>