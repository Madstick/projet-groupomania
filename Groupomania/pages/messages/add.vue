<template>
  <div class="custom-post-container">
    <h1 class="text-center marg-btn">Poster un message</h1>
    <hr />
    <div>
      <div>
        <MsgForm
          button-text="Envoyer"
          :submit-form="submitForm"
          :message="message"
        />
      </div>
    </div>
  </div>
</template>

<script>
import MsgForm from '@/components/MsgForm'
export default {
  middleware: 'auth',
  components: {
    MsgForm,
  },
  data() {
    return {
      message: {
        title: null,
        content: null,
        attachment: null,
        username: this.$auth.user[0].username,
      },
    }
  },
  methods: {
    async submitForm(msgInfo) {
      const formData = new FormData()

      formData.append('title', msgInfo.title)
      formData.append('content', msgInfo.content)
      formData.append('message_parent', null)
      formData.append('username', this.$auth.user[0].username)
      if (msgInfo.attachment) {
        formData.append('attachment', msgInfo.attachment)
      }

      await this.$axios
        .$post('http://localhost:3000/api/messages', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        })
        .then((response) => {
          if (response.id) {
            this.$toast.show('Le message à bien été crée', {
              position: 'bottom-center',
              duration: 2000,
              action: {
                text: 'Fermer',
                onClick: (e, toastObject) => {
                  toastObject.goAway(0)
                },
              },
            })
            this.$router.push('/messages/' + response.id)
          }
        })
        .catch((error) => {
          console.log(error)
          this.$toast.show("Il y'a eu un problème lors de l'envoi du message", {
            position: 'bottom-center',
            duration: 2000,
            action: {
              text: 'Fermer',
              onClick: (e, toastObject) => {
                toastObject.goAway(0)
              },
            },
          })
        })
    },
  },
}
</script>

<style scoped></style>
