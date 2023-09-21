<template>
  <div>
    <h2>Attack Planner</h2>
    <!-- Your Village -->
    <div>
      <label for="x1">Your Village X:</label>
      <input type="number" id="x1" v-model="x1" />
      <label for="y1">Your Village Y:</label>
      <input type="number" id="y1" v-model="y1" />
    </div>

    <!-- Target Villages -->
    <div v-for="(target, index) in targets" :key="index">
      <label :for="'targetX' + index">X:</label>
      <input :type="'number'" :id="'targetX' + index" v-model="target.x" />
      <label :for="'targetY' + index">Y:</label>
      <input :type="'number'" :id="'targetY' + index" v-model="target.y" />
    </div>

    <!-- Add Target Button -->
    <button @click="addTarget">Add Target</button>

    <label for="selectedSpeed">Select Unit Type:</label>
    <select id="selectedSpeed" v-model="selectedSpeed">
      <option value="SPEAR_SPEED">Spear</option>
      <option value="SWORD_SPEED">Sword</option>
      <!-- Add more unit types as needed -->
    </select>

    <div>
      <label for="arrivalHours">Hours:</label>
      <input type="number" id="arrivalHours" v-model="arrivalHours" />
      <label for="arrivalMinutes">Minutes:</label>
      <input type="number" id="arrivalMinutes" v-model="arrivalMinutes" />
      <label for="arrivalSeconds">Seconds:</label>
      <input type="number" id="arrivalSeconds" v-model="arrivalSeconds" />
    </div>
    <button @click="calculateDepartureTimes">Calculate Departure Times</button>

     <!-- Display Results for Each Unit Type and Source Village -->
     <div v-for="(unitResults, unitType) in allResults" :key="unitType">
      <h3>{{ unitType }} Results:</h3>
      <ul>
        <li v-for="(result, index) in unitResults" :key="index">
          Source X: {{ result.source.x }}, Source Y: {{ result.source.y }} - 
          Target X: {{ result.target.x }}, Target Y: {{ result.target.y }} - 
          Departure Time: {{ result.time }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      sx1: 0, // Your village X
      y1: 0, // Your village Y
      targets: [{ x: 0, y: 0 }], // Initialize with one target
      selectedSpeed: "SPEAR_SPEED",
      speeds: {
        SPEAR_SPEED: 14,
        SWORD_SPEED: 18,
        AXE_SPEED: 14,
        ARCHER_SPEED: 14,
        LC_SPEED: 8,
        MA_SPEED: 8,
        HC_SPEED: 9,
        RAM_SPEED: 24,
        CAT_SPEED: 24,
        BERSEK_SPEED: 14,
        TREB_SPEED: 50,
        NOBLE_SPEED: 35,
      },
      arrivalHours: 0,
      arrivalMinutes: 0,
      arrivalSeconds: 0,
      allResults: {}, // Store results for each unit type
      
    };
  },
  methods: {

    function calculateDistance(x1, y1, x2, y2) {
      let dx = x1 - x2;
      let dy = y1 - y2;

      if (dy % 2) {
       dx += y1 % 2 ? 0.5 : -0.5;
      }
    }

  const distance = Math.sqrt(dx * dx + dy * dy * 0.75);

  return distance;
}

    addTarget() {
      // Push a new target object with default values
      this.targets.push({ x: 0, y: 0 });
    },

    calculateDepartureTimes() {
      // Get the selected speed in seconds per unit distance
      const speed = this.speeds[this.selectedSpeed] * 60;

      // Convert the arrival time to seconds
      const arrivalTimeInSeconds =
        this.arrivalHours * 3600 +
        this.arrivalMinutes * 60 +
        this.arrivalSeconds;

      // Create copies of targets to preserve the original coordinates
      const targetsCopy = this.targets.map((target) => ({ ...target }));

      // Calculate departure time for each target
      const results = [];
      for (const target of targetsCopy) {
        // Calculate the distance based on the polygon schema
        const distance = calculateDistance(this.sourceX, this.sourceY, target.x, target.y);

        // Calculate travel time in seconds
        const travelTimeInSeconds = distance * speed;

        // Calculate departure time in seconds
        const departureTimeInSeconds = arrivalTimeInSeconds - travelTimeInSeconds;

        // Convert departure time to hours, minutes, and seconds
        const departureHours = Math.floor(departureTimeInSeconds / 3600);
        const departureMinutes = Math.floor((departureTimeInSeconds % 3600) / 60);
        const departureSeconds = Math.floor(departureTimeInSeconds % 60);

        // Format and add the result to the list
        const formattedDepartureTime = `${departureHours}:${departureMinutes}:${departureSeconds}`;
        results.push({ target, time: formattedDepartureTime });
      }

      // Store the results for the current unit type
      if (!this.allResults[this.selectedSpeed]) {
        this.allResults[this.selectedSpeed] = [];
      }
      this.allResults[this.selectedSpeed] = results.concat(this.allResults[this.selectedSpeed]);
    },
  },
};
</script>
