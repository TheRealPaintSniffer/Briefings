<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "002",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "002",
          "name": "DAYBREAK",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "STORM",
          "alias": "Damian Kane",
          "code": "Kane※.Damian:e5a27297-233f-4090-8399-68486efd0ac4//NDL-C-SORROW-ROYAL",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "SETTING SUN"
        },
         {
          "callsign": "TRADESMAN",
          "alias": "Jorst Wagar",
          "code": "Wagar※※.Jost:190fb16c-d9a6-424a-a85e-82e495fd247b//NDL-C-LUNAR-OCTOBERL",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "THE BELIEVING WIDOW"
        },
        {
          "callsign": "BLACKJACK",
          "alias": "Samuel Bliss",
          "code": ".Samuel.Bliss:f43edfe6-516f-4342-afb2-c8a202a99513//NDL-C-DISCORDANT-AUGUSTa",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "EDEN'S PRIDE"
        },
        {
          "callsign": "Warana",
          "alias": "Raziel Glinski",
          "code": "4be26ce9-923b-4069-b6c9-76437d4be455///NDL-C-DEEP-STATION//056940c6-8d55-4190-8e85-57caa043cb1a",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "YAGUARAÇÚ MK I"
        },
        {
          "callsign": "BEWG",
          "alias": 'Prys Reierson',
          "code": "d1fdf62e-d81e-4e10-97c8-df3bc4860117///NDL-C-DEEP-STATION//5a4254aa-9fa2-42ca-a077-8f5bfd1e1ad3",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "BABY"
        },
         {
          "callsign": "THURSDAY",
          "alias": 'GUNTER STAUSS',
          "code": "Stauss※.Gunter:17083f89-f62c-4d08-b179-2e63cdce92db//NDL-C-THETA-MOUNTAIN",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "TANNGRISNIR"
        },
      ],
      "header": {
        "planet": "Cressidium",
        "year": "5013u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "UNION",
        "headerSubtitle": "NAVAL DEPARTMENT",
        "subheaderTitle": "UNS-CV",
        "subheaderSubtitle": "RIO-GRANDE",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
