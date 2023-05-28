<script>
export default {
  data() {
    return {
      count: 0,
      lines: {
        N: ['Times Square', '34th', '28th', '23rd', 'Union Square', '8th'],
        L: ['8th', '6th', 'Union Square', '3rd', '1st'],
        6: ['Grand Central', '33rd', '28th', '23rd', 'Union Square', 'Astor Place']
      },
      startLine: '',
      startStop: '',
      endLine: '',
      endStop: '',
      startStops: [],
      endStops: [],
      errorMessage: '',
      lineStartResult: [],
      lineEndResult: [],
      totalStops: 0,
      sameStop: false,
      processed: false,
    }
  },

  methods: {
    handleClick() {
      this.planTrip(this.startLine, this.startStop, this.endLine, this.endStop);
    },

    updateStartStops() {
      this.startStops = this.lines[this.startLine];
    },

    updateEndStops() {
      this.endStops = this.lines[this.endLine];
    },

    planTrip(startLine, startStop, endLine, endStop) {

      let usStartIndex = this.lines[startLine].indexOf('Union Square');
      let usEndIndex = this.lines[endLine].indexOf('Union Square');

      let startStopIndex = this.lines[startLine].indexOf(startStop);
      let endStopIndex = this.lines[endLine].indexOf(endStop);

      let lineStart = [...this.lines[startLine]];
      let lineEnd = [...this.lines[endLine]];

      let startLineDirection = '';
      let endLineDirection = '';

      if (startLine === endLine) {
        this.sameStop = startStopIndex === endStopIndex;
        
        if (this.sameStop) {
          this.errorMessage = 'You have arrived, no need to use the Subway.';
        }

        if(startStopIndex < endStopIndex) {
          this.lineStartResult = lineStart.slice((startStopIndex), (endStopIndex + 1));
        } else {
          this.lineStartResult = lineStart.slice((endStopIndex), (startStopIndex + 1 )).reverse();
        }

        this.totalStops = this.lineStartResult.length - 1;
      } else {
        startLineDirection = startStopIndex < usStartIndex ? 'normal' : 'reverse';
        endLineDirection  = endStopIndex > usEndIndex ? 'normal' : 'reverse';
        
        if (startLineDirection === 'normal') {
          this.lineStartResult = lineStart.slice(startStopIndex, usStartIndex + 1);
        } else if (startLineDirection === 'reverse') {
          this.lineStartResult = lineStart.slice(usStartIndex, startStopIndex + 1).reverse();
        }

        if (endLineDirection === 'normal') {
          this.lineEndResult = lineEnd.slice(usEndIndex + 1, endStopIndex + 1);
        } else if (endLineDirection === 'reverse') {
          this.lineEndResult = lineEnd.slice(endStopIndex, usEndIndex).reverse();
        }

        this.totalStops = this.lineStartResult.length + this.lineEndResult.length - 1;
      }
      this.processed = true;
    }
  }
}
</script>

<template>
  <div class="greetings">
    <h3>
      Welcome to MTA Lab! You can start planning your trip here:
      <br>
      Start:
      <select v-model="startLine" @change="updateStartStops">
        <option v-for="(line, index) in lines" :key="line" :value="index">{{ index }}</option>
      </select>
      <select v-model="startStop">
        <option v-for="stop in startStops" :key="stop" :value="stop">{{ stop }}</option>
      </select>
      <br>
      Stop:
      <select v-model="endLine" @change="updateEndStops">
        <option v-for="(stops, line) in lines" :key="line" :value="line">{{ line }}</option>
      </select>
      <select v-model="endStop">
        <option v-for="stop in endStops" :key="stop" :value="stop">{{ stop }}</option>
      </select>
      <br>
      <div v-if="processed">
        <div v-if="sameStop">
          {{ errorMessage }}
        </div>
        <div v-if="startLine === endLine && !sameStop">
          You must travel through the following stops on Line {{ startLine }}:
          <ul>
            <li v-for="stop in lineStartResult">{{ stop }}</li>
          </ul>
          So {{ totalStops }} stops in total.
        </div>

        <div v-else-if="startLine !== endLine && !sameStop">
          You must travel through the following stops on Line {{ startLine }}:
          <ul>
            <li v-for="stop in lineStartResult">{{ stop }}</li>
          </ul>  
          Change at Union Square to Line {{ endLine }}.
          Your journey continues through the followng stops on Line {{ endLine }}:
          <ul>
            <li v-for="stop in lineEndResult">{{ stop }}</li>   
          </ul>
          So {{ totalStops }} stops in total.
        </div>
      </div>
      <br>
      <br>
      <button @click="handleClick">Plan Trip</button>
    </h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
