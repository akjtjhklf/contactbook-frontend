import ContactCard from '@/components/ContactCard.vue'; import ContactList from
'@/components/ContactList.vue'; import InputSearch from
'@/components/InputSearch.vue';

<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>
    <div class="mt-3 col-md-6">
      <h4>
        Danh ba
        <i class="fas fa-address-book"></i>
      </h4>
      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Khong co lien he nao.</p>
      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList()">
          <i class="fas fa-redo"></i> Làm mới
        </button>
        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>
        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>

    <div class="mt-3 col-md-6">
      <div v-if="activecContact">
        <h4>
          Chi tiet lien he
          <i class="fas fa-address-card"></i>
        </h4>
        <ContactCard :contact="activeContact" />
      </div>
    </div>
  </div>
</template>

<script>
import ContactCard from '@/components/ContactCard.vue';
import InputSearch from '@/components/InputSearch.vue';
import ContactList from '@/components/ContactList.vue';
import ContactService from '@/services/contact.service';

export default {
    components: {
        ContactCard,
        InputSearch,
        ContactList,
    },
    data() {
        return {
            contact: [],
            activeIndex: -1,
            searchText: "",
        };
    },
    watch: {
        //
        //
        searchText() {
            this.activeIndex = -1;
        },
    },
    computed: {
        //Chuyen cac doi tuong contact thanh chuoi de cho tien tim kiem
        contactString() {
            return this.contacts.map((contact) => {
                const { name, email, address, phone } = contact;
                return [ name, email, address, phone ].join("");
            });
        },
        filteredContacts() {
            if (!this.searchText) return this.contacts;
            return this.contacts.filter((_contact, index) =>
                this.contactString[index].includes(this.searchText)
            );
        },
        activeContact() {
            if (this.activeIndex < 0) return null;
            return this.filteredContacts[this.activeIndex];
        },
        filteredContactsCount() {
            return this.filteredContacts.length;
        },
    },
    methods: {
        async retrieveContacts() {
            try {
                this.contacts = await ContactService.getAll();
            } catch (error) {
                console.log(error);
            }
        },
        refreshList() {
            this.retrieveContacts();
            this.activeIndex = -1;
        },
        async removeAllContacts() {
            if (confirm("Ban muon xoa tat ca lien he?")) {
                try {
                    await ContactService.deleteAll();
                    this.refreshList;
                } catch (error) {
                    console.log(error);
                }
            }
        },
        goToAddContact() {
            this.$router.push({ name: "contact.add"});
        },
    },
    mounted() {
        this.refreshList();
    },

};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
