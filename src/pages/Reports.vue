<template>
  <div>
    <Header />
    <div class="text-center align-middle">
      <div class="inline-block w-full max-w-screen-xl">
        <div class="text-left">
          <h1 class="p-5 text-2xl md:text-3xl md:ml-3 font-semibold">
            {{ reportData }} in {{ this.$route.params.sessionName }} session
          </h1>
        </div>

        <div class="p-3">
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-indigo-900
              hover:bg-indigo-100
              hover:border-indigo-400
              mx-2
            "
            @click="loadData('messages')"
          >
            Load messages
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/metro/52/000000/chat.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-green-900
              hover:bg-green-100
              hover:border-green-400
              mx-2
            "
            @click="loadData('rights')"
          >
            Load right tries
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/material/48/000000/checkmark--v1.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-red-900
              hover:bg-red-100
              hover:border-red-400
              mx-2
            "
            @click="loadData('wrongs')"
          >
            Load wrong tries
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/material-sharp/48/000000/multiply.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-yellow-900
              hover:bg-yellow-100
              hover:border-yellow-400
              mx-2
            "
            @click="loadData('inputs')"
          >
            Load code additions
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/ios/50/000000/shift-mac.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-purple-900
              hover:bg-purple-100
              hover:border-purple-400
              mx-2
            "
            @click="loadData('deletions')"
          >
            Load code deletions
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/ios/50/000000/delete-key.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-indigo-900
              hover:bg-indigo-100
              hover:border-indigo-400
              mx-2
            "
            @click="loadDataset()"
          >
            Load statistics
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/ios/50/000000/statistics.png"
            />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-indigo-900
              hover:bg-indigo-100
              hover:border-indigo-400
              mx-2
            "
            @click="loadAnalysis()"
          >
            Run analysis
            <img class="w-8 mt-3 mx-auto" img src="https://img.icons8.com/ios/50/000000/run-command.png" />
          </button>
          <button
            class="
              border
              rounded-md
              p-5
              hover:text-indigo-900
              hover:bg-indigo-100
              hover:border-indigo-400
              mx-2
            "
            @click="showAnalysis()"
          >
            Show analysis
            <img class="w-8 mt-3 mx-auto" src="https://img.icons8.com/ios/50/000000/show-property.png" />
          </button>
          <!-- <div>
            <data-table v-bind="parametersTable2" />
          </div> -->
          <div
            v-if="finishAnalysis"
            class="p-3 text-left max-w-4xl mx-auto mb-20 relative"
          >
            <div>
              <data-table v-bind="parametersTable1()" />
            </div>
            <!-- <table
              id="myTable"
              class="display table-bordered nowrap"
              cellspacing="0"
              width="100%"
            >
              <thead>
                <th v-for="title in columnTitles" :key="title">
                  {{ title }}
                </th>
              </thead>
              <tbody v-for="(user, index) in users" v-bind:key="index">
                <tr>
                  <td v-for="values in user" v-bind:key="values">
                    {{ values }}
                  </td>
                </tr>
              </tbody>
            </table> -->
          </div>
        </div>
        <!-- Content -->
        <div
          v-if="finishLoading"
          class="p-3 text-left max-w-4xl mx-auto mb-20 relative"
        >
          <div v-for="(room, index) in participants" :key="index">
            <div v-if="series[index] != null">
              <p>Room {{ index }}:</p>
              <div id="chart">
                <apexchart
                  type="bar"
                  height="350"
                  :options="chartOptions"
                  :series="series[index]"
                ></apexchart>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "../components/Header";
import VueApexCharts from "vue-apexcharts";
import Vue from "vue";
import DataTable from "@andresouzaabreu/vue-data-table";
import "bootstrap/dist/css/bootstrap.min.css";
import "@andresouzaabreu/vue-data-table/dist/DataTable.css";

// import users from "./users.js";

Vue.component("data-table", DataTable);
export default {
  components: {
    Header,
    DataTable,
    apexchart: VueApexCharts,
  },
  computed: {
    // parametersTable1() {
    //   return {
    //     data: users2,
    //     actionMode: "multiple",
    //     columnKeys: [
    //       "id",
    //       "code",
    //       "mail",
    //       "gender",
    //       "birthDate",
    //       "subject",
    //       "beganStudying",
    //       "numberOfSubjects",
    //       "knownLanguages",
    //       "signedUpOn",
    //       "token",
    //       "room",
    //       "token",
    //       "blind",
    //       "jsexp",
    //     ],
    //   };
    // },
    // parametersTable2() {
    //   return {
    //     data: users,
    //     actionMode: "multiple",
    //     columnKeys: [
    //       "id",
    //       "code",
    //       "mail",
    //       "gender",
    //       "birthDate",
    //       "subject",
    //       "beganStudying",
    //       "beganStudying",
    //       "numberOfSubjects",
    //       "knownLanguages",
    //       "signedUpOn",
    //       "token",
    //       "room",
    //       "token",
    //       "blind",
    //       "jsexp",
    //     ],
    //   };
    // },
  },
  data() {
    return {
      tests: [],
      reportData: "Reports",
      series: [],
      participants: [],
      finishLoading: false,
      finishAnalysis: false,
      chartOptions: {
        chart: {
          type: "bar",
          height: 350,
          stacked: true,
          toolbar: {
            show: true,
          },
          zoom: {
            enabled: true,
          },
        },
        responsive: [
          {
            breakpoint: 480,
            options: {
              legend: {
                position: "bottom",
                offsetX: -10,
                offsetY: 0,
              },
            },
          },
        ],
        plotOptions: {
          bar: {
            horizontal: false,
          },
        },
        xaxis: {
          type: "string",
          categories: [],
        },
        legend: {
          position: "right",
          offsetY: 40,
        },
        fill: {
          opacity: 1,
        },
      },
    };
  },
  methods: {
    parametersTable1() {
      var f = [];
      // var users2 = this.users;
      // var users3 = users;
      // console.log(users);
      if (!this.users) {
        alert(
          'The is no analysis, please run analysis by pressing "Run Analysis" button'
        );
        return 0;
      } else {
        this.users.map((x) => f.push(x));

      
        return {
          data: this.users,
          columnKeys: [
            "id",
            "code",
            "mail",
            "gender",
            "birthdate",
            "subject",
            "beganstudying",
            "numberofsubjects",
            "knownlanguages",
            "signedupon",
            "room",
          ],
        };
      }
    },
    loadTests() {
      fetch(
        `${process.env.VUE_APP_TC_API}/tests/${this.$route.params.sessionName}`,
        {
          method: "GET",
          headers: {
            Authorization: localStorage.adminSecret,
          },
        }
      )
        .then((response) => {
          if (response.status == 200) {
            return response.json();
          }
        })
        .then((tests) => {
          if (tests) {
            let orderedTests = [];
            tests.forEach((test) => {
              let orderedTest = {};
              orderedTest.name = test.name;
              orderedTest.exercises = test.exercises.length;
              orderedTests.push(orderedTest);
              for (let i = 0; i < orderedTest.exercises; i++) {
                this.chartOptions.xaxis.categories.push(`${test.name} - E${i}`);
              }
            });
            this.tests = orderedTests;
          }
        });
    },
    loadReport(test, exercise) {
            console.log(exercise);

      fetch(
        `${process.env.VUE_APP_TC_API}/tests/${this.$route.params.sessionName}/${test}/${exercise}/reports`,
        {
          method: "GET",
          headers: {
            Authorization: localStorage.adminSecret,
          },
        }
      )
        .then((response) => {
          if (response.status == 200) {
            return response.json();
          }
        })
        .then((reportsRaw) => {
          reportsRaw.forEach((room, i) => {
            if (this.series.length < reportsRaw.length) this.series.push([]);
            room.forEach((user, u) => {
              if (this.series[i].length < room.length) {
                let totalM = user.totalMessages || 0;
                this.series[i].push({
                  name: user.user,
                  data: [totalM],
                });
              } else {
                let total = user.totalMessages || 0;
                this.series[i][u].data.push(total);
              }
            });
          });
          this.reports.push(reportsRaw);
        });
    },
    loadReports(user, room) {
      fetch(`${process.env.VUE_APP_TC_API}/users/${user}/reports`, {
        method: "GET",
        headers: {
          Authorization: localStorage.adminSecret,
        },
      })
        .then((response) => {
          return response.json();
        })
        .then((response) => {
          if (this.series[room]) {
            this.series[room].push(response);
          } else {
            this.series[room] = [response];
          }
        });
    },
    callData(sessionName, type) {
      fetch(`${process.env.VUE_APP_TC_API}/sessions/${sessionName}/${type}`, {
        method: "GET",
        headers: {
          Authorization: localStorage.adminSecret,
        },
      })
        .then((response) => {
          return response.json();
        })
        .then((response) => {
          this.series = response;
          this.participants = new Array(response.length);
          this.finishLoading = true;
        });
    },
    loadData(type) {
      this.finishLoading = false;
      switch (type) {
        case "messages":
          this.reportData = "Messages";
          break;
        case "rights":
          this.reportData = "Right tries";
          break;
        case "wrongs":
          this.reportData = "Wrong tries";
          break;
        case "inputs":
          this.reportData = "Code additions";
          break;
        case "deletions":
          this.reportData = "Code deletions";
          break;
      }
      this.callData(this.$route.params.sessionName, type);
    },
    loadDataset() {
      fetch(
        `${process.env.VUE_APP_TC_API}/dataset/` +
          this.$route.params.sessionName,
        {
          method: "GET",
          headers: {
            Authorization: localStorage.adminSecret,
          },
        }
      )
        .then((response) => {
          return response.json();
        })
        .then((response) => {
          let filename = this.$route.params.sessionName + ".csv";
          let text = this.$papa.unparse(response);

          let element = document.createElement("a");
          element.setAttribute(
            "href",
            "data:text/csv;charset=utf-8," + encodeURIComponent(text)
          );
          element.setAttribute("download", filename);

          element.style.display = "none";
          document.body.appendChild(element);

          element.click();
          document.body.removeChild(element);
          return response;
        });
    },
    showAnalysis() {
      if (this.finishAnalysis) {
        this.finishAnalysis = false;
      } else {
        this.finishAnalysis = true;
      }
    },
    loadAnalysis() {
      console.log("Loading analysis...");
      fetch(
        `${process.env.VUE_APP_TC_API}/analyze/` +
          this.$route.params.sessionName +
          "/show",
        {
          method: "GET",
          headers: {
            Authorization: localStorage.adminSecret,
          },
        }
      )
        .then((response) => {
          return response.json();
        })
        .then((data) => {
          this.finishAnalysis = true;
          // var $ = require("jquery");

          const usersArray = [];
          const columnTitles = [];
          for (let user in data) {
            var userObj = {};
            for (let key in data[user]) {
                var k = key.toLowerCase();
                userObj[k] = data[user][key];
              // }
            }
            console.log(userObj);
            usersArray.push(userObj);
          }
          for (let title in data[0]) {
            columnTitles.push(title);
          }
          this.users = usersArray;
          this.columnTitles = columnTitles;
        });
    },
  },
  mounted() {
    this.loadTests();
  },
};
</script>

<style></style>
