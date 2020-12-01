<template>
    <base-container>
        <h2>Active Users</h2>
        <BaseSearch @search="updateSearch" :search-term="enteredSearchTerm" />
        <div>
            <button @click="sort('asc')" :class="{selected: sorting === 'asc'}">Sort Ascending</button>
            <button @click="sort('dsc')" :class="{selected: sorting === 'dsc'}">Sort Descending</button>
        </div>
        <ul>
            <UserItem v-for="user in displayedUsers" :key="user.id" :userName="user.fullName" :id="user.id" @view-projects="$emit('list-projects', $event)"/>
        </ul>
    </base-container>
</template>

<script>
import { ref, computed, watch } from 'vue'
import BaseContainer from '../UI/BaseContainer'
import BaseSearch from '../UI/BaseSearch'
import UserItem from './UserItem.vue'
export default {
    components:{
        BaseContainer,
        BaseSearch,
        UserItem
    },
    props:['users'],
    emits:['list-projects'],
    setup(props){
        const enteredSearchTerm = ref('')
        const activeSearchTerm = ref('')

        const availableUsers = computed(function(){
            let users = []
            if(activeSearchTerm.value){
                users = props.users.filter(user => 
                    user.fullName.includes(activeSearchTerm.value)
                )
            } else if (props.users){
                users = props.users
            }
            return users
        })

        function updateSearch(val){
            enteredSearchTerm.value = val
        }

        watch(enteredSearchTerm, function(newValue){
            setTimeout(function(){
                if(newValue === enteredSearchTerm.value){
                    activeSearchTerm.value = newValue
                }
            }, 300)
        })

        const sorting = ref('')

        function sort(mode){
            sorting.value = mode
        }

        const displayedUsers = computed(function(){
            if(!sorting.value){
                return availableUsers.value
            }
            return availableUsers.value.slice().sort((u1, u2) => {
                if(sorting.value ==='asc' && u1.fullName > u2.fullName){
                    return 1
                } else if (sorting.value === 'asc') {
                    return -1
                } else if(sorting.value === 'dsc' && u1.fullName > u2.fullName){
                    return -1
                } else {
                    1
                }
            })
        })

        return { enteredSearchTerm, updateSearch, displayedUsers, sorting, sort }
    }
}
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>