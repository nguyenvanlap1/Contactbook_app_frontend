<template>
  <div class="d-flex justify-content-center">
    <div class="page row">
      <div class="col-md-10">
        <InputSearch v-model="searchText" />
      </div>
      <div class="mt-3 col-md-6">
        <h4>
          Danh bạ
          <i class="fas fa-address-book"></i>
        </h4>
        <ContactList
          v-if="filteredContactsCount > 0"
          :contacts="filteredContacts"
          v-model:activeIndex="activeIndex"
        />
        <p v-else>Không có liên hệ nào.</p>
        <div class="mt-3 d-flex justify-content-around align-items-center">
          <button class="btn btn-sm btn-primary" @click="refreshList">
            <i class="fa-sharp fa-thin fa-arrows-rotate"></i> Làm mới
          </button>
          <button class="btn btn-sm btn-success" @click="goToAddContact">
            <i class="fas fa-plus"></i> 
            <router-link
            style="color: aliceblue;"
            :to="{
              name: 'newcontact',
            }">Thêm mới
          </router-link>
          </button>
          <button class="btn btn-sm btn-danger" @click="removeAllContacts">
            <i class="fas fa-trash"></i> Xóa tất cả
          </button>
        </div>
      </div>
      <div class="mt-3 col-md-6">
        <div v-if="activeContact">
          <h4>
            Chi tiết Liên hệ
            <i class="fas fa-address-card"></i>
          </h4>
          <ContactCard :contact="activeContact" />
          <router-link
            :to="{
              name: 'contact.edit',
              params: { id: activeContact._id },
            }"
          >
          <span class="mt-2 badge badge-warning edit-button">
            <i class="fas fa-edit"></i> Hiệu chỉnh
          </span>

          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ContactCard from '@/components/ContactCard.vue';
import InputSearch from '@/components/InputSearch.vue';
import ContactList from '@/components/ContactList.vue';
import contactServices from '@/services/contact.services';

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },
  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: '',
    };
  },
  watch: {
    searchText() {
      this.activeIndex = -1;
    },
  },
  computed: {
    contactStrings() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join(' ');
      });
    },
    filteredContacts() {
      if (!this.searchText) return this.contacts;
      return this.contacts.filter((_contact, index) =>
        this.contactStrings[index].includes(this.searchText)
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
        this.contacts = await contactServices.getAll();
      } catch (error) {
        console.log(error);
      }
    },
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },
    async removeAllContacts() {
      if (confirm('Bạn muốn xóa tất cả liên hệ')) {
        try {
          await contactServices.deleteAll();
          this.refreshList();
        } catch (error) {
          console.log(error);
        }
      }
    },
    async goToAddContact() {
      this.$router.push({ name: 'contact.add' });
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
.edit-button {
  font-size: 1.2em; /* Increase font size */
  padding: 10px;    /* Add padding */
  background-color: #ffc107; /* Ensure it has a distinct background color */
  border-radius: 5px; /* Add border radius for rounded edges */
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2); /* Add a subtle shadow */
  cursor: pointer; /* Change cursor to pointer */
  transition: background-color 0.3s ease; /* Smooth transition on hover */
}

.edit-button:hover {
  background-color: #e0a800; /* Slightly darker color on hover */
}

</style>
