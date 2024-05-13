<template>
    <q-page padding>
        <q-form
            class="row q-col-gutter-md"
            @submit="onSubmit"
        >
            <q-input
                class="col-lg-6 col-xs-12"
                outlined
                v-model="form.title"
                label="Título"
                lazy-rules
                :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
            />

            <q-input
                class="col-lg-6 col-xs-12"
                outlined
                v-model="form.author"
                label="Autor"
                lazy-rules
                :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
            />
            <div class="col-12">
                <q-editor v-model="form.content" min-height="5rem" />
            </div>

            <div class="col-12 q-gutter-sm">
                <q-btn
                    class="float-right"
                    color="primary"
                    label="Salvar"
                    icon="save"
                    type="submit"
                />
                <q-btn
                    class="float-right"
                    color="white"
                    text-color="primary"
                    label="Cancelar"
                    :to="{ name: 'home' }"
                />
            </div>
        </q-form>
    </q-page>
</template>

<script>
import {defineComponent, onMounted, ref} from "vue";
import postsService from "src/services/posts";
import {useQuasar} from "quasar";
import {useRoute, useRouter} from "vue-router";

export default defineComponent({
    name: 'FormPost',
    setup() {
        const { post, getById, update } = postsService();
        const $q = useQuasar();
        const router = useRouter();
        const route = useRoute();

        const form = ref({
            title: '',
            author: '',
            content: '',
        });

        onMounted(async () => {
            if (route.params.id) {
                await getPost(route.params.id)
            }
        });

        const getPost = async (id) => {
            try {
                form.value = await getById(id);
            } catch (error) {
                console.error(error);
            }
        };

        const onSubmit = async () => {
            try {
                if (form.value.id){
                    await update(form.value)
                } else {
                    await post(form.value);
                }
                $q.notify({
                    color: 'positive',
                    message: 'Post criado com sucesso!',
                    icon: 'check'
                })
                await router.push({name: 'home'});
            } catch (error) {
                console.error(error);
            }
        };

        return {
            form,
            onSubmit,
        };
    }
});
</script>
