<template>
  <div class="container mx-auto px-4 my-6 w-full md:w-md lg:w-lg xl:w-xl xxl:w-2xl">
    <div class="text-grey-dark font-semibold text-2xl mb-4 flex items-center justify-center">
      {{ team.name }}
      <span @click="toggleDropdownMenu" v-click-outside="closeDropdownMenu" class="bg-white p-1 text-sm rounded-full shadow ml-4 cursor-pointer flex items-center">
        <font-awesome-icon :icon="faCog"></font-awesome-icon>
      </span>
      <div v-if="dropdownMenuShown" class="relative">
        <ul class="list-reset bg-white rounded shadow-lg py-2 absolute pin-r mt-4 text-base text-left font-normal whitespace-no-wrap">
          <li class="px-4 py-2 hover:bg-grey-light cursor-pointer">
            Show All Members
          </li>
          <li class="px-4 py-2 hover:bg-grey-light cursor-pointer">
            Delete
          </li>
        </ul>
      </div>
    </div>

    <!-- Add Member Form -->
    <addMemberForm v-if="addMemberFormShown" @close="closeAddMemberForm" resourceType="team" :resource="team" @addMember="addMember"></addMemberForm>

    <div class="h-16 flex flex-row justify-center items-center px-2">
      <span @click="showAddMemberForm" class="bg-white shadow w-8 h-8 rounded-full text-teal hover:cursor-pointer text-center p-2">
        <font-awesome-icon :icon="faPlus"></font-awesome-icon>
      </span>
      <a v-for="(member, index) in team.members" v-if="index < 5" :href="'/users/' + member.username" class="pl-2">
        <img :src="generateUrl(member.avatar)" class="rounded-full w-8 h-8 mr-1">
      </a>
      <span v-if="team.members.length > 5" class="bg-grey-lighter border-teal border p-2 rounded-full">{{ team.members.length - 5 }}+</span>
    </div>

    <tab-menu :active="active" @activate="activateTab"></tab-menu>

    <div class="flex flex-row flex-wrap justify-center">
      <taskBoard resourceType="team" :resource="team"  :activeTab="active"></taskBoard>
      <discussionBoard resourceType="team" :resource="team" :activeTab="active"></discussionBoard>
      <messagesBoard resourceType="team" :resource="team" :activeTab="active"></messagesBoard>
      <eventBoard resourceType="team" :resource="team" :activeTab="active"></eventBoard>
      <file-board resourceType="team" :resource="team" :activeTab="active"></file-board>
      <!-- <schedule resourceType="projects" :resource="project"></schedule>
      <activity resourceType="projects" :resource="project"></activity> -->
    </div>
  </div>
</template>

<script>
import taskBoard from './../partials/taskBoard.vue'
import discussionBoard from './../partials/discussionBoard.vue'
import messagesBoard from './../partials/messagesBoard.vue'
import eventBoard from './../partials/eventBoard.vue'
import fileBoard from './../partials/fileBoard.vue'
import activity from './../partials/activity.vue'
import addMemberForm from './../partials/addMemberForm.vue'
import tabMenu from './../partials/tabMenu.vue'
import { faPlus } from '@fortawesome/free-solid-svg-icons/faPlus'
import { faCog } from '@fortawesome/free-solid-svg-icons/faCog'

export default {
  components: {
    taskBoard, discussionBoard, messagesBoard, eventBoard, fileBoard, activity, addMemberForm, tabMenu
  },
  props: ['team'],
  data: () => ({
    addMemberFormShown: false,
    active: 'tasks',
    dropdownMenuShown: false,
    faPlus,
    faCog
  }),
  created () {
    let tool = new URL(location.href).searchParams.get('tool')
    this.active = tool
  },
  methods: {
    showAddMemberForm () {
      this.addMemberFormShown = true
    },
    closeAddMemberForm () {
      this.addMemberFormShown = false
    },
    addMember (data) {
      let messageType
      if (data.user) {
        messageType = 'success'
        this.team.members.push(data.user)
      } else {
        messageType = 'error'
      }
      EventBus.$emit('notification', data.message, messageType)
      this.addMemberFormShown = false
    },
    activateTab (tab) {
      if (tab !== this.active) {
        this.active = tab
      }
    },
    toggleDropdownMenu () {
      this.dropdownMenuShown = !this.dropdownMenuShown
    },
    closeDropdownMenu () {
      this.dropdownMenuShown = false
    }
  }
}
</script>
