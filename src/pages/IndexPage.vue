<template>
    <q-page padding>
        <div class="q-pa-md">
            <q-table
                title="Posts"
                :rows="posts"
                :columns="columns"
                row-key="name"
                :loading="loading"
                :pagination="{ rowsPerPage: 10 }"
            >
                <template v-slot:top>
                    <span class="text-h5">Posts</span>
                    <q-space/>
                    <q-btn color="primary" label="Novo post" :disable="loading" :to="{ name: 'formPost' }"/>
                </template>
                <template v-slot:body-cell-actions="props">
                    <q-td :props="props" class="q-gutter-x-sm">
                        <q-btn
                            dense
                            color="blue"
                            icon="edit"
                            @click="editPost(props.row)"
                        />
                        <q-btn
                            dense
                            color="negative"
                            icon="delete"
                            @click="deletePost(props.row)"
                        />
                    </q-td>
                </template>
            </q-table>
        </div>
    </q-page>
</template>

<script>
import {defineComponent, onMounted, ref} from 'vue'
import postsService from 'src/services/posts'
import { useQuasar} from "quasar";
import {useRouter} from "vue-router";

export default defineComponent({
    name: 'IndexPage',

    setup() {
        const posts = ref([])
        const loading = ref(false)
        const $q = useQuasar()
        const router = useRouter()

        const {list, remove} = postsService()
        const columns = [
            {name: 'id', label: 'ID', align: 'left', field: 'id', sortable: true},
            {name: 'title', label: 'Título', align: 'left', field: 'title', sortable: true},
            {name: 'author', label: 'Autor', align: 'left', field: 'author', sortable: false},
            {name: 'content', label: 'Conteúdo', align: 'left', field: 'content', sortable: false},
            {name: 'actions', label: 'Ações', align: 'right', field: 'actions', sortable: false},
        ]

        onMounted(() => {
            getPosts()
        })

        const getPosts = async () => {
            try {
                posts.value = await list()
            } catch (error) {
                console.error(error)
            }
        }

        const editPost = async (row) => {
            await router.push({name: 'formPost', params: { id: row.id }})
        }

        const deletePost = async (row) => {
            try {
                $q.dialog({
                    title: 'Deletar post',
                    message: 'Você tem certeza que deseja deletar este post?',
                    persistent: true,
                    ok: {
                        label: 'Sim',
                        color: 'negative'
                    },
                    cancel: {
                        label: 'Não',
                        color: 'grey-8'
                    }
                }).onOk(async () => {
                    await remove(row.id)
                    $q.notify({
                        color: 'positive',
                        message: 'Post deletado com sucesso!',
                        icon: 'check'
                    })
                    await getPosts()
                })
            } catch (error) {
                $q.notify({
                    color: 'negative',
                    message: 'Erro ao deletar o post!',
                    icon: 'error'
                })
            }
        }

        return {
            posts,
            loading,
            columns,
            editPost,
            deletePost
        }
    }
})
</script>
