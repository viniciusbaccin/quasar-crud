<template>
    <q-layout view="hHh Lpr lFf">
        <q-header elevated>
            <q-toolbar>
                <q-btn
                    flat
                    dense
                    round
                    icon="menu"
                    aria-label="Menu"
                    @click="toggleLeftDrawer"
                />

                <q-toolbar-title>
                    CRUD Quasar
                </q-toolbar-title>

                <div>Quasar v{{ $q.version }}</div>
            </q-toolbar>
        </q-header>

        <q-drawer
            v-model="leftDrawerOpen"
            bordered
        >
            <q-list>
                <q-item-label
                    header
                >
                    Menu
                </q-item-label>

                <MenuDrawer
                    v-for="link in menuItems"
                    :key="link.title"
                    v-bind="link"
                />
            </q-list>
        </q-drawer>

        <q-page-container>
            <router-view/>
        </q-page-container>
    </q-layout>
</template>

<script>
import {defineComponent, ref} from 'vue'
import MenuDrawer from 'components/MenuDrawer.vue'

const menuList = [
    {
        title: 'Home',
        caption: '',
        icon: 'home',
        route: {name: 'home'}
    },
]

export default defineComponent({
    name: 'MainLayout',

    components: {
        MenuDrawer
    },

    setup() {
        const leftDrawerOpen = ref(false)

        return {
            menuItems: menuList,
            leftDrawerOpen,
            toggleLeftDrawer() {
                leftDrawerOpen.value = !leftDrawerOpen.value
            }
        }
    }
})
</script>
