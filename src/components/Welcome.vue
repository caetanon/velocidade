<template lang="pug">
  #welcome
    // Previous Sprint name
    .screen(v-if="isShowingPreviousSprint")
      h2 Dados da sua sprint anterior:
      .previousSprint
        label(for="previousSprintDays")
          = "Quantos dias tiveram na Sprint Anterior"
          input(type="number" name="previousSprintDays" v-model="previousSprintDays")
        label(for="previousSprintPoints")
          = "Quantos dias tiveram na Sprint Anterior"
          input(type="number" name="previousSprintPoints" v-model="previousSprintPoints")
      p.buttons
        button(@click="showSprintName") Próximo
      p(v-if="feedback").error {{ feedback }}
    
    // Sprint name
    .screen(v-if="isShowingSprintName")
      label(for="sprintName") Qual o nome da sua sprint?
      input(type="text" name="sprintName" v-model="sprint.name")
      p.buttons
        button(@click="showPreviousSprint") Anterior
        button(@click="showHollidays") Próximo
      p(v-if="feedback").error {{ feedback }}

    // Hollidays
    .screen(v-if="isShowingHollidays")
      label(for="hollidays") Quantos feriados acontecerão durante a sprint?
      input(type="number" name="hollidays" v-model="hollidays")
      p.buttons
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
      p.totalTime Tempo total em cerimônias:
        strong {{ totalCerimonyTime }}h
      p.buttons
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
            th Ausências em dias (sem contar os feriados)
        tbody
          tr(v-for="(member, index) in team" :key="'table'+index")
            td: input(type="text" v-model="team[index].teamMember")
            td: input(type="number" v-model="team[index].hoursPerDay")
            td: input(type="number" v-model="team[index].absense")
      button(@click="addTeamMember") Adicionar membro ao time
      p.buttons
        button(@click="showHollidays") Anterior
        button(@click="showResult") Próximo
      p(v-if="feedback").error {{ feedback }}

    .screen(v-if="isShowingResult")
      h1 {{ sprint.name }}
      p Story Points: {{ totalSprintStoryPoints }} pts
      p Feriados: {{ hollidays }} dias
      p Cerimônias: {{ totalCerimonyTime }} horas
      p Capacity: {{ sprintCapacityDays }} dias

      .cards
        .card(v-for="(member, index) in validTeam" :key="index")
          h2 {{ member.teamMember }}
          p {{ member.hoursPerDay }} horas/dias
          p {{ member.absense ? member.absense : 'Sem' }} {{ member.absense > 1 ? 'ausências programadas' : 'ausência programada' }} 
          p Working Days: {{ member.workingDays }} dias
          p Capacity: {{ member.capacityHours }} horas | {{ member.capacityDays }} dias
</template>

<script>
export default {
  name: 'Welcome',
  data(){
    return {
      feedback: null,

      isShowingPreviousSprint: true,
      previousSprintDays: 10,
      previousSprintPoints: 40,
      
      sprint: {
        name: '',
        capacityDays: 0,
      },
      isShowingSprintName: false,

      hollidays: 0,
      isShowingHollidays: false,

      totalCerimonyTime: 10,
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
      team: [
        {
          teamMember: '😎',
          hoursPerDay: 6,
          absense: 0
        }
      ],

      isShowingResult: false,
      totalSprintStoryPoints: 0
    }
  },
  methods: {
    hideAllScreens(){
      this.isShowingPreviousSprint = false;
      this.isShowingSprintName = false;
      this.isShowingHollidays = false;
      this.isShowingCerimonies = false;
      this.isShowingTeam = false;
    },
    showPreviousSprint(){
      this.hideAllScreens();
      this.isShowingPreviousSprint = true;
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
    showResult(){
      this.hideAllScreens();
      this.isShowingResult = true;
    },

    sumCerimoniesTime(){
      this.totalCerimonyTime = 0;
      this.totalCerimonyTime += this.aep ? this.time.aep : 0;
      this.totalCerimonyTime += this.planning ? this.time.planning : 0;
      this.totalCerimonyTime += this.grooming ? this.time.grooming : 0;
      this.totalCerimonyTime += this.prereview ? this.time.prereview : 0;
      this.totalCerimonyTime += this.review ? this.time.review : 0;
    },
    addTeamMember(){
      this.team.push({
        teamMember: '',
        hoursPerDay: 6,
        absense: 0,
      })
    },
  
  },
  computed: {
    sprintWorkingDays(){
      return 10 - this.hollidays;
    },
    validTeam() {
      return this.team.map(member => {
        const validMember = { ...member };
        validMember.workingDays = this.sprintWorkingDays - validMember.absense;
        validMember.capacityHours = (validMember.workingDays*validMember.hoursPerDay)-this.totalCerimonyTime;
        validMember.capacityDays = validMember.capacityHours/8;
        return validMember;
      });
      
    },
    sprintCapacityDays(){
      return this.validTeam.reduce((acc,member) => acc+parseFloat(member.capacityDays), 0)
    }
  }
}
</script>

<style lang="scss" scoped>
.previousSprint {
  display: grid;
  grid-gap: 2rem;
  grid-template-rows: 1fr 1fr;
  padding: 3rem 0;
  
  label {
    display: inline-block;
    
    input {
      display: block;
      width: 100%;
    }
  }
}
</style>
