<template>
  <div id="app">
    <nav><h1>Wino</h1></nav>
    <div class="container stuff">
      <div class="divider"></div>
      <div class="btn-wine" @click="showModal = true">Add Wine</div>
    </div>
    <div class="container flex-container">
      <div class="box" v-for="(item, id) in items" :key="id">
        <img :src="item.image" :alt="item.name">
        <h4>
          <span id="itemName">{{ item.name }}</span>
          <span style="float:right" v-if="item.rating == 0">
            <i class="far fa-frown"></i>
          </span>
          <span style="float:right" v-else-if="item.rating == 1">
            <i class="far fa-meh"></i>
          </span>
          <span style="float:right" v-else-if="item.rating == 2">
            <i class="far fa-smile"></i>
          </span>
          <span style="float:right" v-else-if="item.rating == 3">
          <i class="fas fa-heart"></i>
        </span>
        </h4>
        <div class="divider"></div>
        <p>
          <span>{{ item.shop }}</span>
          <span style="float:right">{{ item.price }}€</span>
        </p>

      </div>
    </div>

    <Modal v-if="showModal" @close="showModal = false">
      <form slot="body" @submit.prevent="addItem" id="add-form">
        <div class="form-group">
          <label for="exampleInputPassword1">Name</label>
          <input type="text" class="form-control" placeholder="Blancbois" v-model="itemName">
        </div>
        <div class="form-group">
          <label for="exampleInputPassword1">Price</label>
          <input type="text" class="form-control" placeholder="2.19€" v-model="price">
        </div>
        <div class="form-group">
          <label for="exampleInputPassword1">Shop</label>
          <input type="text" class="form-control" placeholder="Penny" v-model="shop">
        </div>
        <div class="form-group">
          <label for="exampleInputPassword1">Image</label>
          <input type="file" @change="uploadImage">
        </div>
        <div class="form-group rating">
          <label style="margin-top: 5px;">Did you like it?</label>
          <div class="form-check">
            <input v-model="rating" class="form-check-input" type="radio" id="ratingFrown" value="0" checked>
            <label class="form-check-label" for="ratingFrown">
              <i :class="[rating == 0 ? 'fas' : 'far']" class="fa-frown fa-2x"></i>
            </label>
          </div>
          <div class="form-check">
            <input v-model="rating" class="form-check-input" type="radio" id="ratingMeh" value="1" checked>
            <label class="form-check-label" for="ratingMeh">
              <i :class="[rating == 1 ? 'fas' : 'far']" class="fa-meh fa-2x"></i>
            </label>
          </div>
          <div class="form-check">
            <input v-model="rating" class="form-check-input" type="radio" id="ratingSmile" value="2" checked>
            <label class="form-check-label" for="ratingSmile">
              <i :class="[rating == 2 ? 'fas' : 'far']" class="fa-smile fa-2x"></i>
            </label>
          </div>
          <div class="form-check">
            <input v-model="rating" class="form-check-input" type="radio" id="ratingHeart" value="3" checked>
            <label class="form-check-label" for="ratingHeart">
              <i :class="[rating == 3 ? 'fas' : 'far']" class="fa-heart fa-2x"></i>
            </label>
          </div>
        </div>
        <input class="btn-wine" style="display: block" type="submit" value="Submit">
      </form>
    </Modal>
  </div>
</template>

<script>
  import Firebase from './initFirebase'
import Modal from './components/Modal.vue'

const db  = Firebase.firestore()

  Firebase.auth().signInAnonymously().catch(function(error) {
    // Handle Errors here.
    var errorCode = error.code;
    var errorMessage = error.message;
    // ...
  });

export default {
  name: 'app',
  data() {
    return {
    showModal: false,
    itemName: null,
    shop: null,
    rating: null,
    price: null,
    image: null,
    items: [],
  }},
  methods: {
    addItem() {
      let parsedPrice = parseFloat(this.price.replace(',', '.'));
      let newItem = new Object({
        name: this.itemName,
        shop: this.shop,
        price: parsedPrice,
        rating: this.rating,
        image: this.image
      });
      db.collection('items').add(newItem);
      //this.items.push(newItem);
      this.itemName = null;
      this.shop = null;
      this.price = null;
      this.rating = null;
      this.image = null;
      this.showModal = false;
    },
    removeItem(id) {
      db.collection('items').doc(id).delete()
    },
    uploadImage(e) {
      let file = e.target.files[0];
      var storageRef = Firebase.storage().ref('images/' + file.name);
      let uploadTask = storageRef.put(file);
      uploadTask.on('state_changed',
        () => {
          uploadTask.snapshot.ref.getDownloadURL().then((downloadURL) => {
            console.log("PLS" + downloadURL);
            this.image = downloadURL;
          })
        }
      )}
  },
  created() {
    this.$bind('items', db.collection('items')).then(items => {
      this.items === items
      //...
      //this.$unbind(‘items’)
    });
  },
  firestore: {
    items: db.collection('items'),
  },
  components: {
    Modal
  },
}
</script>

<style lang="scss">
  body {
    background-color: #582335;
    color: #FFEBA7;
    font-family: 'Dosis', sans-serif;
  }
  input[type=radio] {
    display: none;
  }
  .form-control {
    font-size: 16px;
  }
  nav {
    width: 100%;
    text-align: center;
    margin-top: 20px;
    font-family: 'Poiret One', sans-serif;
  }
  .divider {
    border-bottom: 1px solid #FFEBA7;
  }
  .flex-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }
  .box {
    border: 1px solid grey;
    margin: 10px;
    padding: 15px;
    background: #FFEBBB;
    color: #582335;
  }
  .box p, .box h4 {
    padding-top: 10px;
  }
  .box .divider {
    border-bottom: 1px solid #582335 !important;
  }
  .btn-wine {
    color: #FFEBA7;
    border: 1px solid #FFEBA7;
    border-radius: 0px;
    padding: 5px 10px;
    margin: auto;
    text-align: center;
    width: 150px;
    background-color: #582335;
  }
  img {
    width: 233px;
    height: 300px;
    object-fit: cover;
  }
  @media (max-width: 992px) and (min-width: 510px){
    img {
      width: 188px;
    }
    .box #itemName {
      display: inline-block;
      max-width: 150px;
    }
  }
  .stuff .divider {
    margin: auto;
    margin-bottom: 10px;
  }
  .rating {
    display: inline-flex;
  }
  .rating .form-check {
    margin-left: 5px;
    margin-right: 5px;
  }
  /***Modal***/
  .modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .3s ease;
    color: black;
  }

  .modal-wrapper {
    display: table-cell;
    vertical-align: middle;
  }

  .modal-container {
    width: 500px;
    margin: 0px auto;
    padding: 15px 20px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
    transition: all .3s ease;
    font-family: Dosis, sans-serif;
  }
  @media (max-width: 560px) {
    .modal-container {
      width: 90%;
    }
  }
  .modal-body {
    margin: 20px 0;
  }

  .modal-default-button {
    float: right;
  }

  .modal-enter {
    opacity: 0;
  }

  .modal-leave-active {
    opacity: 0;
  }

  .modal-enter .modal-container,
  .modal-leave-active .modal-container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
  }
</style>
