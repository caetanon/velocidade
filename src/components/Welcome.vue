<template lang="pug">
  #welcome
    // Sprint name
    .screen(v-if="isShowingSprintName")
      label(for="sprintName") Qual o nome da sua sprint?
      input(type="text" name="sprintName" v-model="sprintName")
      p
        button(@click="showHollidays") Próximo
      p(v-if="feedback").error {{ feedback }}

    // Hollidays
    .screen(v-if="isShowingHollidays")
      label(for="hollidays") Quantos feriados acontecerão durante a sprint?
      input(type="number" name="sprintName" v-model="sprintName")
      p
        button(@click="showSprintName") Anterior
        button(@click="showCerimonies") Próximo
      p(v-if="feedback").error {{ feedback }}

    // Cerimonies
    .screen(v-if="isShowingCerimonies")
      label Quais cerimônias acontecem durante a sprint?
      .selectionBox
        label
          input(type="checkbox" v-model="aep" @change="sumCerimoniesTime")
          = "A&P"
          span ({{ time.aep }}h)
        label
          input(type="checkbox" v-model="planning" @change="sumCerimoniesTime")
          = "Planning"
          span  ({{ time.planning }}h)
        label
          input(type="checkbox" v-model="grooming" @change="sumCerimoniesTime")
          = "Grooming"
          span  ({{ time.grooming }}h)
        label
          input(type="checkbox" v-model="prereview" @change="sumCerimoniesTime")
          = "Pre Review"
          span ({{ time.prereview }}h)
        label
          input(type="checkbox" v-model="review" @change="sumCerimoniesTime")
          = "Review"
          span  ({{ time.review }}h)
      p Tempo total em cerimônias {{ totalCerimonyTime }}h
      p
        button(@click="showHollidays") Anterior
        button(@click="showTeam") Próximo
      p(v-if="feedback").error {{ feedback }}

    // Team members
    .screen(v-if="isShowingTeam")
      label Quais cerimônias acontecem durante a sprint?
      table
        thead
          tr
            th Membro do Time
            th Horas/dia
            th Ausências em dias
        tbody
          tr
            td: input(type="text" v-model="teamMember" placeholder="digite o nome")
            td: input(type="number" v-model="hoursPerDay")
            td: input(type="number" v-model="absense")
      p
        button(@click="showHollidays") Anterior
        button(@click="showSprintName") Próximo
      p(v-if="feedback").error {{ feedback }}
</template>

<script>
export default {
  name: 'Welcome',
  data(){
    return {
      feedback: null,

      sprintName: null,
      isShowingSprintName: true,

      hollidays: 0,
      isShowingHollidays: false,

      totalCerimonyTime: 8,
      isShowingCerimonies: false,
      aep: true,
      planning: true,
      grooming: false,
      prereview: false,
      review: true,
      time: {
        aep: 4,
        planning: 2,
        grooming: 2,
        prereview: 1,
        review: 4,
      },

      isShowingTeam: false,
      team: [],
      teamMember: '',
      hoursPerDay: 8,
      absense: 0,
    }
  },
  methods: {
    hideAllScreens(){
      this.isShowingSprintName = false;
      this.isShowingHollidays = false;
    },
    showSprintName(){
      this.hideAllScreens();
      this.isShowingSprintName = true;
    },
    showHollidays(){
      this.hideAllScreens();
      this.isShowingHollidays = true;
    },
    showCerimonies(){
      this.hideAllScreens();
      this.isShowingCerimonies = true;
    },
    showTeam(){
      this.hideAllScreens();
      this.isShowingTeam = true;
    },

    sumCerimoniesTime(){
      this.totalCerimonyTime = 0;
      this.totalCerimonyTime += this.aep ? this.time.aep : 0;
      this.totalCerimonyTime += this.planning ? this.time.planning : 0;
      this.totalCerimonyTime += this.grooming ? this.time.grooming : 0;
      this.totalCerimonyTime += this.prereview ? this.time.prereview : 0;
      this.totalCerimonyTime += this.review ? this.time.review : 0;
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
