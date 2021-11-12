<template>
  <div id="app">
    <img alt="Vue logo" src="https://vuejs.org/images/logo.png" />
    <input type="text" v-model="url" />
    <button @click="loadTextFromFile(url)">EXEMPLE</button>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import excel from 'fast-xlsx-reader';

export default {
  name: 'App',
  components: {
    HelloWorld,
  },
  data() {
    return {
      url: 'https://test-csv-icf.s3.eu-west-1.amazonaws.com/example.xlsx',
    };
  },
  methods: {
    loadTextFromFile(tmppath) {
      console.log('tmppath', tmppath);

      console.time('READ EXCEL');

      // Create an instance of the FastXlsxSheetReader class
      const reader = excel.createReader({
        // input: 'C:/Users/fernando.blanco/Desktop/my-excel-app/src/data/excel-file-to-read.xlsx.xlsx'
        input: tmppath,
      });
      console.log('reader', reader);

      let totalRowCount = 0;

      // read all sheets
      rowCount = reader.readAllSheets(
        /* classic function */ function (name) {
          // inside a classic JavaScript function (NOT an arrow function)
          // the 'this' keyword refers to the 'reader': this === reader (true)
          console.log(
            `Reading ${name} (${this.rowCount} rows)...`.toUpperCase()
          );
          console.log();

          totalRowCount += this.rowCount;
        },
        /* arrow function */ (row, index) => {
          console.log(`#${index + 1}`, row);

          if ((index + 1) % 10 === 0) {
            // returning true aborts the operation
            return true;
          }
        }
      );

      // verify that not all rows have been read
      console.info(`Total rows in all worksheets: ${totalRowCount}`);
      console.info(`Rows read from all worksheets: ${rowCount}`);

      // clean up
      reader.destroy();

      console.timeEnd('READ EXCEL');
      // reader.reset().readNext(); // throws an error because we destroyed the reader
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
