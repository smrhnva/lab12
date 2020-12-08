<template>
  <div id="app">
    <div class="container">
      <div>
        <div class="form-group">
          <label for="">Фамилия</label>
          <input type="text" class="form-control" v-model="contNow.surname">
        </div>

        <div class="form-group">
          <label>Имя</label>
          <input type="text" class="form-control" v-model="contNow.name">
        </div>

        <div class="form-group">
          <label>Отчество</label>
          <input type="text" class="form-control" v-model="contNow.otchestvo">
        </div>

        <div class="form-group">
          <label>Телефон</label>
          <input type="text" class="form-control" v-model="contNow.phone">
        </div>

        <div class="form-group">
          <label>Избранное <input type="checkbox" v-model="contNow.fav"></label>
        </div>

      </div>
        <button class="btn btn-primary" v-on:click="saveContact()">Записать</button>
        <button class="btn btn-primary ml-2" v-on:click="clearForm()">Очистить</button>
      <div class="mt-2 mb-5">
        <h3 class="font-weight-bold text-center">Сортировать</h3>
        <div class="row">
          <p class="col text-center">По фамилии</p>
          <input class="col" type="radio" name="sort" value="surname" v-on:click="contactsSort('surname')">
          
        </div>
        <div class="row">
          <p class="col text-center">По имени</p>
          <input class="col" type="radio" name="sort" value="name" v-on:click="contactsSort('name')">
        </div>
      </div>
      <div class="row mt-2" v-for="contact in contacts" v-bind:key="contact.id">
        <div class="col-2">{{contact.surname}}</div>
        <div class="col-2">{{contact.name}}</div>
        <div class="col-2">{{contact.otchestvo}}</div>
        <div class="col-2">{{contact.phone}}</div>
        <div class="col-1"><span v-if="contact.fav">❤</span></div>
        <div class="col-2"><button class="btn btn-warning" v-on:click="fillForm(contact)">Изменить</button></div>
        <div class="col-1"><button class="btn btn-danger" v-on:click="deleteContact(contact.id)">X</button></div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "contacts",
    data() {
      return {
        contNow: {
          surname: "",
          name: "",
          otchestvo: "",
          phone: "",
          fav: false,
        },
        contacts: [],
        inIdCont: -1, // array
        outIdCont: -1, // database
      }
    },
    methods: {
      saveContact() {
        if (this.contNow.surname != "" && this.contNow.name != "" && this.contNow.phone != "") {
          if (this.inIdCont === -1 && this.outIdCont === -1) {
            this.addContact();
          }
          else {
            this.changeContact();
          }
          this.clearForm();
        }
        else {
          alert("Заполните поля с данными: Фамилия, Имя, Телефон.");
        }
      },
      async addContact() {
        let contact = {
          surname: this.contNow.surname,
          name: this.contNow.name,
          otchestvo: this.contNow.otchestvo,
          phone: this.contNow.phone,
          fav: this.contNow.fav
        };
        try {
          await this.$http.post("http://localhost:3000/contacts", contact);
          this.updateContactsList();
        }
        catch(err) {
          console.error(err);
        }
      },
      async changeContact() {
        this.contacts[this.inIdCont] = {
          surname: this.contNow.surname,
          name: this.contNow.name,
          otchestvo: this.contNow.otchestvo,
          phone: this.contNow.phone,
          fav: this.contNow.fav
        }
        try {
          await this.$http.put("http://localhost:3000/contacts/" + this.outIdCont, this.contacts[this.inIdCont]);
          this.updateContactsList();
        }
        catch(err) {
          console.error(err);
        }
        this.inIdCont = -1;
        this.outIdCont = -1;
      },
      contactsSort : function (typeSort) {
        if (typeSort === "surname") {
          this.contacts.sort((c1, c2) => c1.surname < c2.surname ? 1 : -1);
        }
        else {
          if (typeSort === "name") {
            this.contacts.sort((c1, c2) => c1.name < c2.name ? 1 : -1);
          }
        }
        this.contacts.sort((c1, c2) => c1.fav < c2.fav ? 1 : -1);
      },
      async deleteContact(id) {
        try {
          await this.$http.delete("http://localhost:3000/contacts/" + id);
          this.updateContactsList();
        }
        catch(err) {
          console.error(err);
        }
      },
      async updateContactsList() {
        try {
          let res = await this.$http.get("http://localhost:3000/contacts");
          this.contacts = await res.json();
        }
        catch(err) {
          console.error(err);
        }
      },
      fillForm(contact) {
        this.outIdCont = contact.id;
        this.contNow.surname = contact.surname;
        this.contNow.name = contact.name;
        this.contNow.otchestvo = contact.otchestvo;
        this.contNow.phone = contact.phone;
        this.contNow.fav = contact.fav;
        this.inIdCont = this.contacts.findIndex(item => item.phone === contact.phone);
      },
      clearForm() {
        this.contNow.surname = "";
        this.contNow.name = "";
        this.contNow.otchestvo = "";
        this.contNow.phone = "";
        this.contNow.fav = false;
      }
    },
    created() {
      this.updateContactsList();
    }
  }
</script>
