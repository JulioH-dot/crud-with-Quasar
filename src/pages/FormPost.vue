<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-md"
    >
    <q-input
        outlined
        v-model="form.title"
        label="Title"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo Obrigatório']"
      />
      <q-input
        outlined
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo Obrigatório']"
      />
      <div class="col-lg-12 col-xs-12">
        <q-editor
          v-model="form.content"
          min-height="5rem"
        />
      </div>
      <div class="col-12 q-gutter-sm">
        <q-btn
        label="Salvar"
        color="primary"
        class="float-right"
        icon="save"
        type="submit"
        >
        </q-btn>
        <q-btn
          label="Cancelar"
          color="white"
          class="float-right"
          text-color="primary"
          icon="cancel"
          :to="{ name: 'home' }"
          >
        </q-btn>
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useRouter, useRoute } from 'vue-router'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const form = ref({
      title: '',
      author: '',
      content: ''
    })

    const $q = useQuasar()
    const { post, getById, update } = postsService()
    const router = useRouter()
    const routeId = useRoute()

    onMounted(async () => {
      if (routeId.params.id) {
        getPostId(routeId.params.id)
      }
    })

    const getPostId = async (id) => {
      try {
        const data = await getById(id)
        form.value = data
      } catch (error) {
        console.log(error)
      }
    }

    const onSubmit = async () => {
      try {
        $q.dialog({
          title: 'Salvar',
          message: 'Deseja salvar este item?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          if (routeId.params.id) {
            await update(form.value)
            $q.notify({ message: 'Post atualizado com sucesso', icon: 'check', color: 'positive' })
          } else {
            await post(form.value)
            $q.notify({ message: 'Post salvo com sucesso', icon: 'check', color: 'positive' })
          }
          router.push({ name: 'home' })
        })
      } catch (error) {
        console.log(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
