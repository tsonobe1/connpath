<template>
  <div>
    <div class="goal">
      <h2 class="goal__title">📈 {{ goal.goal }}</h2>
      <h3 class="goal__detail">{{ goal.deets }}</h3>
    </div>

    <br />

    <h3>
      📌 Condition of Success
      <button
        @click="cosAddDialog = !cosAddDialog"
        class="button__design material-icons"
        style="vertical-align: -5px"
      >
        add_circle
      </button>
    </h3>
    <div v-if="goal.cos" class="condition__content_left-margin">
      <div v-for="(condition, index) in goal.cos" :key="condition.id">
        <template v-if="condition.cmplt">✔ : </template>
        <template v-else>□</template>
        <span style="position: relative"> {{ condition.cond }}</span>
        <span style="position: absolute; right: 10%">
          <button
            @click="deleteCondition(condition, goal.docId)"
            class="button__design  material-icons"
          >
            delete
          </button>

          <button
            @click="showUpdateCondition(condition.cond, index)"
            class="button__design material-icons"
          >
            edit
          </button>
        </span>
      </div>
    </div>
    <template v-else>
      <p>Not Exist Condition of Success</p>
    </template>

    <TaskList></TaskList>

    <!-- Update Conditon of Dialog -->
    <vs-dialog width="" not-center v-model="cosUpdateDialog">
      <template #header>
        <h4>
          <p>{{ cosUpdateBaseInput }}</p>
        </h4>
      </template>
      <div>
        <vs-input
          v-model="cosUpdateNewInput"
          placeholder="condition of success"
        ></vs-input>
      </div>
      <template #footer>
        <div>
          <vs-button
            @click="
              (cosUpdateDialog = false), updateCondition(cosUpdateNewInput, nth)
            "
            transparent
          >
            Ok
          </vs-button>
          <vs-button @click="cosUpdateDialog = false" dark transparent>
            Cancel
          </vs-button>
        </div>
      </template>
    </vs-dialog>

    <!-- Add Conditon of Dialog -->
    <vs-dialog width="300px" not-center v-model="cosAddDialog">
      <template #header>
        <h4>Input Condition of Success</h4>
      </template>
      <div>
        <vs-input
          v-model="cosAddInput"
          placeholder="condition of success"
        ></vs-input>
      </div>
      <template #footer>
        <div>
          <vs-button
            @click="
              (cosAddDialog = false), addCondition(cosAddInput, goal.docId)
            "
            transparent
          >
            Ok
          </vs-button>
          <vs-button @click="cosAddDialog = false" dark transparent>
            Cancel
          </vs-button>
        </div>
      </template>
    </vs-dialog>

    <!-- <pre>goal: {{ goal }}</pre> -->
    <!-- <pre>selectingGoal :{{ $store.state.selectingGoal }}</pre> -->
  </div>
</template>

<script lang="ts">
import Vue from "vue";
const Vuesax = require("vuesax");
import { db, firestore } from "@/main";
import TaskList from "@/views/Goal/Task/TaskList.vue";

interface goalObjectType {
  goal: string;
  cos: Array<{
    cond: string;
    cmplt: boolean;
  }>;
  deets: string;
  cre_at: {
    seconds: number;
    nanoseconds: number;
  };
  docId: string;
}

interface dataType {
  goal: goalObjectType;
  cosAddDialog: boolean;
  cosAddInput: string;
  cosUpdateDialog: boolean;
  cosUpdateBaseInput: string;
  cosUpdateNewInput: string;
  nth: number;
}

export default Vue.extend({
  components: { TaskList },
  props: {},
  data(): dataType {
    return {
      goal: {
        goal: "",
        cos: [],
        deets: "",
        cre_at: {
          seconds: 0,
          nanoseconds: 0,
        },
        docId: "",
      },
      cosAddDialog: false,
      cosAddInput: "",
      cosUpdateDialog: false,
      cosUpdateBaseInput: "",
      cosUpdateNewInput: "",
      nth: 0,
    };
  },
  methods: {
    addCondition: function(cond: string, docId: string) {
      const vm = this;
      const userUId = vm.$store.state.user.uid;
      const docRef = db.doc(`users/${userUId}/goals/${docId}`);
      console.log(docRef);
      docRef
        .update({
          cos: firestore.FieldValue.arrayUnion({
            cond: cond,
            cmplt: false,
          }),
        })
        .then((result) => {
          console.log(result);
          vm.cosAddInput = "";
        })
        .catch((err) => {
          console.log(err);
        });
    },
    deleteCondition: function(cond: string, docId: string) {
      const vm = this;
      const userUId = vm.$store.state.user.uid;
      const docRef = db.doc(`users/${userUId}/goals/${docId}`);
      docRef.update({
        cos: firestore.FieldValue.arrayRemove(cond),
      });
    },
    showUpdateCondition: function(cond: string, index: number) {
      this.cosUpdateDialog = true;
      this.cosUpdateBaseInput = cond;
      this.nth = index;
    },
    updateCondition: function(cond: string, nth: number) {
      const vm = this;
      const docId = vm.goal.docId;
      const userUId = vm.$store.state.user.uid;
      const docRef = db.doc(`users/${userUId}/goals/${docId}`);
      let originalCos = vm.goal.cos;
      originalCos[nth] = { cond: cond, cmplt: false };
      docRef
        .update({ cos: originalCos })
        .then((el) => {
          console.log(el);
          vm.cosUpdateNewInput = "";
        })
        .catch((err) => console.log(err));
    },
  },
  created() {
    const vm = this;
    const goalId = vm.$route.params.id;
    const userId = vm.$store.state.user.uid;
    const docRef = db.doc(`users/${userId}/goals/${goalId}`);
    docRef.onSnapshot(function(doc) {
      const docData = doc.data();
      if (docData) {
        docData.docId = goalId;
        vm.goal = docData as goalObjectType;
        vm.$store.commit("setSelectingGoal", vm.goal);
        console.log("Firebaseにアクセスしてデータを取得しました");
      } else {
        console.log(Error);
      }
    });
  },
});
</script>

<style lang="scss" scoped>
.goal__title {
  color: #50c38f;
  padding-left: 1em;
  text-indent: -1em;
}
.goal__detail {
  font-weight: 500;
  margin: 0em 0em 0em 3em;
}
.condition__content_left-margin {
  margin: 0em 0em 0em 3em;
}
.button__design {
  border: none;
  background: none;
  color: #4c4c4c;
}
.button__design:hover,
.button__design:focus {
  color: #50c38f;
  transition: 0.2s;
}
</style>