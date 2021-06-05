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
            class="border rounded-md p-5 hover:text-indigo-900 hover:bg-indigo-100 hover:border-indigo-400 mx-2"
            @click="loadData('messages')"
          >
            Load messages
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/metro/52/000000/chat.png"
            />
          </button>
          <button
            class="border rounded-md p-5 hover:text-green-900 hover:bg-green-100 hover:border-green-400 mx-2"
            @click="loadData('rights')"
          >
            Load right tries
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/material/48/000000/checkmark--v1.png"
            />
          </button>
          <button
            class="border rounded-md p-5 hover:text-red-900 hover:bg-red-100 hover:border-red-400 mx-2"
            @click="loadData('wrongs')"
          >
            Load wrong tries
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/material-sharp/48/000000/multiply.png"
            />
          </button>
          <button
            class="border rounded-md p-5 hover:text-yellow-900 hover:bg-yellow-100 hover:border-yellow-400 mx-2"
            @click="loadData('inputs')"
          >
            Load code additions
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/ios/50/000000/shift-mac.png"
            />
          </button>
          <button
            class="border rounded-md p-5 hover:text-purple-900 hover:bg-purple-100 hover:border-purple-400 mx-2"
            @click="loadData('deletions')"
          >
            Load code deletions
            <img
              class="w-8 mt-3 mx-auto"
              src="https://img.icons8.com/ios/50/000000/delete-key.png"
            />
          </button>
        </div>
        <!-- Content -->
        <div
          v-if="finishLoading"
          class="p-3 text-left max-w-4xl mx-auto mb-20 relative"
        >
          <div
            class="border p-3 rounded-md"
            v-for="(room, index) in participants"
            :key="index"
          >
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
</template>
<script>
// /**
//  *
//  * @param objArray
//  */
// function convertToCSV(objArray) {
//   const array = typeof objArray != "object" ? JSON.parse(objArray) : objArray;
//   let str = "";
//   for (let i = 0; i < array.length; i++) {
//     let line = "";
//     for (let index in array[i]) {
//       if (line != "") line += ",";
//       line += array[i][index];
//     }
//     str += line + "\r\n";
//   }
//   return str;
// }

// /**
//  *
//  * @param headers
//  * @param items
//  * @param fileName
//  */
// function exportCSVFile(headers, items, fileName) {
//   if (headers) {
//     items.unshift(headers);
//   }
//   const jsonObject = JSON.stringify(items);
//   const csv = convertToCSV(jsonObject);
//   const exportName = fileName + ".csv" || "export.csv";
//   const blob = new Blob([csv], { type: "text/csv;charset=utf-8;" });
//   if (navigator.msSaveBlob) {
//     navigator.msSaveBlob(blob, exportName);
//   } else {
//     const link = document.createElement("a");
//     if (link.download !== undefined) {
//       const url = URL.createObjectURL(blob);
//       link.setAttribute("href", url);
//       link.setAttribute("download", exportName);
//       link.style.visibility = "hidden";
//       document.body.appendChild(link);
//       link.click();
//       document.body.removeChild(link);
//     }
//   }
// }

// const headers = {
//  id: 'Identificador',
//  nombre: 'Nombre'
// };
// const data = [
//  { id: 1, nombre: 'John Doe' },
//  { id: 2, nombre: 'Juan' },
//  { id: 3, nombre: 'Samanta' }
// ];
// exportCSVFile(headers, data, 'nombres');
</script>
<script>
import Header from "../components/Header";
import VueApexCharts from "vue-apexcharts";
export default {
  components: {
    Header,
    apexchart: VueApexCharts,
  },
  data() {
    return {
      tests: [],
      reportData: "Reports",
      series: [],
      participants: [],
      finishLoading: false,
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
          this.series = response;
          this.participants = new Array(response.length);
          this.finishLoading = true;

          var type = `${type}`;
          var json2csv = require("json2csv").parse;
          // if(type =)
          var fields = [
            "studentCode",
            "session",
            "room",
            "1-1",
            "1-2",
            "2-1",
            "2-2",
            "3-1",
          ];
          var data = json2csv({
            data: this.series,
            fields: fields,
          });
          return response.json();
        })
        .then((response) => {
          console.log(response);
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
  },
  mounted() {
    this.loadTests();
  },
};
</script>

<style></style>
