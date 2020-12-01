<template>
    <base-container v-if="user">
        <h3>{{ user.fullName}} : Projects </h3>
        <BaseSearch v-if="hasProjects" @search='updateSearch' :search-term="enteredSearchTerm" />
        <ul v-if="hasProjects">
            <ProjectItem v-for="project in availableProjects" :key="project.id" :title="project.title"/>
        </ul>
        <h3 v-else>No projects found.</h3>
    </base-container>
    <base-container v-else>
        <h3>No users found.</h3>
    </base-container>
</template>

<script>
import { ref, computed, watch, toRefs } from 'vue'
import BaseContainer from '../UI/BaseContainer.vue'
import BaseSearch from '../UI/BaseSearch.vue'
import ProjectItem from './ProjectItem.vue'
export default {
    components:{
        BaseContainer,
        BaseSearch,
        ProjectItem
    },
    props:['user'],
    setup(props){
        const enteredSearchTerm = ref('')
        const activeSearchTerm = ref('')

        const availableProjects = computed(function(){
            if (activeSearchTerm.value){
                return props.user.projects.filter((project) => 
                    project.title.includes(activeSearchTerm.value)
                )
            }
            return props.user.projects
        })

        const hasProjects = computed(function(){
            return props.user.projects && props.user.projects.length > 0
        })

        function updateSearch(val){
            enteredSearchTerm.value = val
        }

        watch(enteredSearchTerm, function(newValue){
            setTimeout(function(){
                if (newValue === enteredSearchTerm.value){
                    activeSearchTerm.value = newValue
                }
            }, 300)
            
        })

        const { user } = toRefs(props)

        watch(user, function(){
            enteredSearchTerm.value = ''
        })

        return { enteredSearchTerm, updateSearch, hasProjects, availableProjects}
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