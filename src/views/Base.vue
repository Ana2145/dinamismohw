<template>
    <div>
      <div class="drop-zone" @drop="onDrop($event, 1)" @dragover.prevent @dragenter.prevent>
        <div v-for="item in listOne" :key="item.title" class="drag-el" :draggable="!isFormEmpty && item.id === 0" @dragstart="startDrag($event, item)">
          <template v-if="item.id === 0 && showForm">
            <form @submit.prevent="submitForm" class="custom-form">
              <label for="name">Autor:</label>
              <input type="text" id="name" v-model="formData.name" required>
              <label for="title">Título:</label>
              <input type="text" id="title" v-model="formData.title" required>
              <label for="price">Precio:</label>
              <input type="number" id="price" v-model="formData.price" required>
              <button type="submit" :disabled="isSubmitting" class="submit-button">
                <template v-if="isSubmitting">
                  <span class="spinner"></span> Enviando...
                </template>
                <template v-else>
                  Registrar
                </template>
              </button>
            </form>
          </template>
        </div>
      </div>
      
      <div class="drop-zone" @drop="onDrop($event, 2)" @dragover.prevent @dragenter.prevent>
        <div v-for="item in listTwo" :key="item.title" class="drag-el2">
          <template v-if="item.list === 2">
            <form class="custom-form">
              <label for="name">Autor:</label>
              <input type="text" :value="item.formData.name" readonly>
              <label for="title">Título:</label>
              <input type="text" :value="item.formData.title" readonly>
              <label for="price">Precio:</label>
              <input type="number" :value="item.formData.price" readonly>
            </form>
          </template>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        listTwoTitle: "Registros realizados", 
        items: [
          {
            id: 0,
            title: "Registro de libro",
            list: 1,
            formData: {
              name: '',
              title: '',
              price: null
            }
          },
          {
            id: 2,
            title: "Item C",
            list: 2,
            formData: {
              name: 'Libro de Brujas',
              title: 'El libro de las sombras',
              price: 200
            }
          },
        ],
        formData: {
          name: '',
          title: '',
          price: null
        },
        isFormEmpty: true, 
        showForm: true, 
        isSubmitting: false, 
        lastScrollPosition: 0
      };
    },
    mounted() {
      window.addEventListener("scroll", this.onScroll);
    },
    beforeDestroy() {
      window.removeEventListener("scroll", this.onScroll);
    },
    methods: {
      startDrag(evt, item) {
        if (!this.isFormEmpty) {
          evt.dataTransfer.dropEffect = "move";
          evt.dataTransfer.effectAllowed = "move";
          evt.dataTransfer.setData("itemID", item.id);
        }
      },
      onDrop(evt, list) {
        const itemID = evt.dataTransfer.getData("itemID");
        const item = this.items.find((item) => item.id == itemID);
        if (item && list === 2 && !this.isFormEmpty) {
          console.log('Registrado:', item.title);
          this.isSubmitting = true;
          setTimeout(() => {
            item.list = list;
            this.items = this.items.filter(i => i.id !== itemID);
            this.isSubmitting = false;
          }, 1500);
        } else if (item && list === 2 && this.isFormEmpty) {
          console.log('Formulario no completo.');
        }
      },
      submitForm() {
        console.log('Formulario enviado:', this.formData);
        if (!this.isEmpty(this.formData)) {
          this.isSubmitting = true;
          setTimeout(() => {
            // Simulación de proceso de registro
            this.items.push({
              id: Date.now(),
              title: "Registro de libro",
              list: 2,
              formData: { ...this.formData }
            });
            this.formData = {
              name: '',
              title: '',
              price: null
            };
            this.isFormEmpty = true;
            this.isSubmitting = false;
          }, 1500); // 1.5 segundos
        }
      },
      isEmpty(formData) {
        return formData.name === "" || formData.title === "" || formData.price === null;
      },
      onScroll() {
        // Obtiene la posición actual del scroll
        const currentScrollPosition = window.pageYOffset || document.documentElement.scrollTop;
        if (Math.abs(currentScrollPosition - this.lastScrollPosition) < 60) {
          return;
        }
        this.showForm = currentScrollPosition < this.lastScrollPosition;
        //  
        this.lastScrollPosition = currentScrollPosition;
      },
    },
    computed: {
      listOne() {
        return this.items.filter((item) => item.list === 1);
      },
      listTwo() {
        return this.items.filter((item) => item.list === 2);
      },
    },
  };
  </script>
  
  <style>
  .drop-zone {
    background-color: #eee;
    margin-bottom: 20px;
    padding: 10px;
  }
  
  .drag-el {
    background-color: #dde9ee;
    margin-bottom: 10px;
    padding: 5px;
  }
  
  .drag-el2 {
    background-color: #a1ddf2;
    margin-bottom: 10px;
    padding: 5px;
  }
  
  .custom-form {
    max-width: 400px;
    margin: auto;
  }
  
  .custom-form label {
    display: block;
    margin-bottom: 5px;
  }
  
  .custom-form input {
    width: 100%;
    padding: 8px;
    margin-bottom: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  .custom-form input[readonly] {
    background-color: #f3f3f3;
  }
  
  .spinner {
    border: 4px solid rgba(0, 0, 0, 0.1);
    border-left-color: #7983ff;
    border-radius: 50%;
    width: 24px;
    height: 24px;
    animation: spin 1s linear infinite;
    display: inline-block;
    margin-right: 8px;
  }
  
  .submit-button {
    display: block;
    width: 100%;
    padding: 10px;
    margin-top: 20px;
    background-color: #7983ff;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .submit-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  
  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
  </style>
  