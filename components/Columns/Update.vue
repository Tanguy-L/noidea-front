<template>
  <div
    :class="showAllUpdate ? 'container-column' : 'container-empty'"
    @mouseover="showTools = true"
    @mouseleave="showTools = false"
  >
    <div v-if="showAllUpdate" class="column-update">
      <Bar v-if="allTasks > 0" :value="tasksDone" :total="allTasks" />
      <h3 class="margin-height-24 ">{{ currentUpdate.name }}</h3>
      <p class="text-full-width text-white">
        {{ currentUpdate.date | formatDate }}
      </p>
      <p class="text-full-width text-white">
        {{ currentUpdate.versionProject }}
      </p>
      <CategoryCard
        v-for="(el, index) in currentUpdate.categories"
        :id="idUpdate"
        :key="index"
        :category="el"
      />
      <ModalCategory
        v-if="showModalCategory"
        :id-update="idUpdate"
        @closeModal="showModalCategory = false"
      />
      <BaseButton
        text-button="Ajouter une catégorie"
        @click.native="showModalCategory = true"
      />
    </div>
    <div v-else class="column-hide">
      <h3 class="margin-height-24 rotate">{{ currentUpdate.name }}</h3>
    </div>
    <div class="container-tools">
      <transition name="fadeTools">
        <div v-if="showTools">
          <button class="tools-button red" @click="removeProject(idUpdate)">
            <i class="material-icons">delete_forever</i>
          </button>
          <button class="tools-button blue">
            <i class="material-icons">edit</i>
          </button>
          <button
            class="tools-button green"
            @click="showAllUpdate = !showAllUpdate"
          >
            <i v-if="showAllUpdate" class="material-icons">navigate_before</i>
            <i v-if="!showAllUpdate" class="material-icons">navigate_next</i>
          </button>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import CategoryCard from "@/components/Cards/CategoryCard.vue";
import ModalCategory from "@/components/Modals/ModalCategory.vue";
import Bar from "@/components/Base/BaseProgressionBar.vue";
import BaseButton from "@/components/Base/BaseButton.vue";
import { mapGetters, mapActions } from "vuex";

export default {
  components: {
    CategoryCard,
    ModalCategory,
    Bar,
    BaseButton
  },
  filters: {
    formatDate: function(value) {
      if (!value) return "";
      let currentDate = new Date(value);
      let monthNames = [
        "Janvier",
        "Février",
        "Mars",
        "Avril",
        "Mai",
        "Juin",
        "Juillet",
        "Août",
        "Septembre",
        "Octobre",
        "Novembre",
        "Décembre"
      ];

      let day = currentDate.getDate();
      let monthIndex = currentDate.getMonth();
      let year = currentDate.getFullYear();

      if (day === 1) {
        day = "1er";
      }

      return day + " " + monthNames[monthIndex] + " " + year;
    }
  },
  props: {
    idUpdate: {
      type: String,
      default: () => {
        return "";
      }
    }
  },
  data() {
    return {
      showModalCategory: false,
      showTools: false,
      showAllUpdate: true
    };
  },
  computed: {
    ...mapGetters(["categories", "updateByIndex", "updates"]),
    currentUpdate() {
      return this.updateByIndex(this.idUpdate);
    },
    tasksDone() {
      const result = this.currentUpdate.tasks.filter(e => e.done === true);
      return result.length;
    },
    allTasks() {
      return this.currentUpdate.tasks.length;
    }
  },
  methods: {
    ...mapActions(["removeProject"])
  }
};
</script>

<style scoped>
.rotate {
  transform: rotate(-90deg);
}

.column-update {
  overflow-y: hidden;
  width: 380px;
  display: inline-block;
  background-color: #242e3e;
  box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.308);
}

.container-column {
  width: 420px;
  margin-left: 50px;
  display: flex;
}

.column-hide {
  background-color: #242e3e;
  height: 250px;
  display: inline-block;
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 80px;
}

.column-hide > h3 {
  margin: 0;
}

.tools-button {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 40px;
}

.text-full-width {
  width: 100%;
  text-align: center;
}

.container-tools {
  width: 40px;
}

.add-category {
  background-color: #242e3e00;
  display: block;
  margin-left: auto;
  margin-right: auto;
  transition: all 0.3s;
  border: 1px white solid;
  height: 75px;
  width: 235px;
}

.add-category:hover {
  cursor: pointer;
  background-color: rgba(70, 133, 228, 0.589);
}

.add-category:focus {
  outline: 0;
}

.delete-category {
  position: absolute;
  right: 0;
  top: 40px;
  color: white;
}

.container-empty {
  height: 800px;
  margin-left: 50px;
  display: flex;
  flex-wrap: wrap;
}

.delete-category:hover {
  cursor: pointer;
}

.column-tools {
  background-color: aqua;
  height: 100%;
  width: 35px;
  position: absolute;
  top: 0;
  right: -35px;
}

.fadeTools-enter-active,
.fadeTools-leave-active {
  transition: opacity 0.6s;
}
.fadeTools-enter, .fadeTools-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
