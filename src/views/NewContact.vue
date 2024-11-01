<template>
  <div class="page">
    <h4>Thêm liên hệ mới</h4>
    <ContactForm 
      :contact="contact"
      @submit:contact="updateContact" 
      @delete:contact="deleteContact" 
    />
    <p>{{ message }}</p>
  </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.services";

export default {
  components: {
    ContactForm,
  },
  props: {
    id: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      contact: {
        name: "",
        email: "",
        address: "",
        favorite: false
      },
      message: "",
    };
  },
  async created() {
    await this.getContact(this.id);
    this.message = "";
  },
  methods: {
    async getContact(id) {
      try {
        this.contact = await ContactService.get(id);
        console.log(this.contact);
      } catch (error) {
        console.log(error);
        // Chuyển sang trang NotFound đồng thời giữ cho URL không đổi
        this.$router.push({
          name: "notfound",
          params: {
            pathMatch: this.$route.path.split("/").slice(1),
          },
          query: this.$route.query,
          hash: this.$route.hash,
        });
      }
    },
    async updateContact(data) {
      try {
        await ContactService.create(data);
        alert('Liên hệ được Thêm mới thành công.');
        this.$router.push({ name: "contactbook" });
      } catch (error) {
        console.log(error);
      }
    },
    async deleteContact() {
      if (confirm("Bạn muốn xóa Liên hệ này?")) {
        try {
          await ContactService.delete(this.contact._id);
          this.$router.push({ name: "contactbook" });
        } catch (error) {
          console.log(error);
        }
      }
    },
  },
};
</script>

<!-- <template>
    <p>Hi</p>
</template> -->
