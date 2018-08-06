<template>
  <div id="blurrr">
   <button id='help' @click='helpVisible = true'>Help</button>
  <!-- Header  -->
  <h2>Your Business:</h2>

  <!-- "Staff" header -->
  <h3>Staff:</h3>

  <!-- Staff Buttons -->

  <button v-for="(staff, index) in staffs" @click='openStaff(index)'>{{ staff.label }}</button>

  <br><br>

  <!-- Staff type and # -->
  <p>{{staffs[activeStaff].label}}: {{shorten(staffs[activeStaff].quantity)}}</p>
  <p class='smallText'>(1 costs: {{staffs[activeStaff].cost}} {{staffs[activeStaff].payment}} and 1 Position)</p>
  <!-- get staff buttons -->
  <div>
    <button v-for="quantity in getQuantities" @click='getSomeStaff(quantity)' :disabled='buttonDisabled(quantity)'>Get {{ quantity }}</button>
  </div>

  <br>

  <!--Money text & # -->
    <div id='money'>
      <p>{{resources[0].label}}
         {{shorten(resources[0].quantity)}}
      </p>
      <p class='smallText'>Rate: {{shorten(this.staffs[0].quantity)}} money/second.</p>
    </div>

    <!--Position text & # -->
    <div id='positions'>
      <p>{{resources[1].label}}
         {{shorten(resources[1].quantity)}}
      </p>
      <p class='smallText'>Rate: {{resources[1].upgrade}} position(s)/second.</p>
      <p id='position' class='smallText'>Upgrades: {{resources[1].upgraded}}</p>
      <p id='position' class='smallText'>Next upgrade: {{shorten(resources[1].cost)}} Money.</p>

      <!-- resource upgrade button -->
      <div >
        <button id='get-button' @click='upgradeResource' :disabled='!canUpgrade'>Upgrade</button>
      </div>

      <br><br>
      <button id='reset' @click='resetVisible = true'>Reset Progress</button>
    </div>
    <br>
    <div class="overlay" :class='{visible: resetVisible}' style='background-color: darkred;'>Are you sure?
      <button @click='resetProgress'>Yep!</button>
      <button @click='resetVisible = false'>No! I didn't mean to press that!!!</button>
    </div>
    <div class="overlay" :class='{visible: winVisible}'>You Win! <br> Continue? <br><br>
      <button @click='winVisible = false'>Yep!</button>
      <button @click='resetProgress'>Nah...no thanks...(I'm boring like that...)</button>
    </div>
    <div class='overlay' style='top: 0px; background-color: LemonChiffon; max-height: 335px; overflow: scroll;' :class='{visible: helpVisible}'>
      <b style='font-size: x-large; '>Insurmountable</b>
      <br>
      <br>
      <br>
      <b>Objective:</b> <br>
      The objective of the game is to obtain a "Chairman" <br>
      <br>
      <br>
      <b>How to Play:</b> <br>
      <br>
      Money: <br>
      Money is used to buy pretty much everything. You can receive money based on the number of "interns" you have. <br>
      <br>
      Positions: <br>
      Positions are used to get your Staff. If you don't have a job to give your staff... it really doesn't make sense to hire them. <br>
      At the beginning of the game, you get 1 "Position" each second; but you can increase the number of positions per second by pressing the upgrade button below the "Position" heading. At the beginning of the game it costs 100 Money to upgrade, and doubles at each upgrade. <br>
      <br>
      Intern: <br>
      The "Intern" is the only "Staff" that only costs "Money" and "Positions". An "Intern" costs 10 "Money" and 1 "Position". You can get "Interns" with the "Get 1", "Get 10" and "Get 50" buttons. Each "Intern" earns you 1 Money each second. <br>
      <br>
      The rest of the "Staff": <br>
      The rest of the "Staff" are all basically the same. An "Employee" costs 100 "Interns" ("Interns" being the "Staff"that is directly before "Employee"), 100 "Money" and 1 "Position"; The "Supervisor" (being the next "Staff" After "Employee") would cost 100 "Employees", 100 "Money" and 1 "Position". The rest of the "Staff" work like-wise. <br>
      <br>
      <b>Also:</b> <br>
      Each "Employee" will give you a free "Intern" every second. and each "Supervisor" will give you a free "Employee" every second... and so on. <br>
      <br>
      <button style='float: center;' @click='helpVisible = false'>Back</button>
    </div>

   <div id='anker'></div>

    <p id='copyright' class='smallText'>
     &copy; Peter Gedeon 2018
    </p>
    </div>
</template>

<script>
export default {
  name: "Game",
  data () {
    return {
  	activeStaff: 0,
    staffs: [
    	{
        label: 'Intern',
        quantity: 0,
        cost: 10,
        payment: 'Money'
      },
    	{
        label: 'Employee',
        quantity: 0,
        cost: 100,
        payment: 'Interns'
      },
    	{
        label: 'Supervisor',
        quantity: 0,
        cost: 100,
        payment: 'Employees'
      },
    	{
        label: 'Director',
        quantity: 0,
        cost: 100,
        payment: 'Supervisors'
      },
    	{
        label: 'Vice President',
        quantity: 0,
        cost: 100,
        payment: 'Directors'
      },
      {
        label: 'Executive',
        quantity: 0,
        cost: 100,
        payment: 'Vice Presidents'
      },
      {
        label: 'CEO',
        quantity: 0,
        cost: 100,
        payment: 'Executives'
      },
      {
        label: 'Chairman',
        quantity: 0,
        cost: 100,
        payment: "CEO's"
      }
    ],
  	getQuantities: [1, 10, 50],
    activeResource: 0,
    resources: [
    	{
      	label: 'Money',
        quantity: 10
      },
      {
      	label: 'Position',
        quantity: 1,
        upgrade: 1,
        upgraded: 0,
        cost: 100
      }
    ],
    numberSuffix: ['k','M','B','T','Qd','Qt','Sxt','Spt','Oct', 'Nan', 'Googal', 'Googalplex', 'Gazillion', 'Insurmountable'],
    resetVisible: false,
    winVisible: false,
    helpVisible: false,
    buttonColor: 'grey'
    }
  },
  created() {
    setInterval(this.increase, 1000);
  },
  computed: {
    canUpgrade: function() {
    	return this.resources[0].quantity >= this.resources[1].cost;
    }
  },
  methods: {
		openStaff: function(index) {
    	this.activeStaff = index
    },
    getSomeStaff: function(quantity) {
      var money = this.resources[0],
          position = this.resources[1],
          staff = this.staffs[this.activeStaff],
          sub = this.staffs[this.activeStaff-1]

    	if(this.activeStaff === 0) {
      	if(money.quantity >= staff.cost * quantity && position.quantity >= quantity) {
        	staff.quantity += quantity;
          money.quantity -= staff.cost * quantity;
          position.quantity -= quantity
        }

      }else if(sub.quantity >= staff.cost * quantity && money.quantity >= staff.cost * quantity && position.quantity >= quantity){
      	staff.quantity += quantity;
        sub.quantity -= staff.cost * quantity;
        money.quantity -= staff.cost * quantity;
        position.quantity -= quantity;
        if(this.activeStaff === 7 && this.staffs[7].quantity === 1){
          this.winVisible = true;
        }
      }
    },
    openResource: function(index) {
    	this.activeResource = index
    },
    getSomeResource: function(quantity) {
        this.resources[this.activeResource].quantity += quantity
    },
    increase: function() {
      this.resources[0].quantity += this.staffs[0].quantity;
      this.resources[1].quantity += this.resources[1].upgrade;
      for(var i = 0; i <= 6; i++){
      	this.staffs[i].quantity += this.staffs[i + 1].quantity
      }
    },
    resetProgress: function() {

    	this.activeStaff = 0;
      for(var i = 0; i < 7; i++){
      	this.staffs[i].quantity = 0;
      }
      this.activeResource = 0;
      this.resources[0].quantity = 10;
      this.resources[1].quantity = 1;
      this.resources[1].upgrade = 1;
      this.resources[1].cost = 100;
      this.resources[1].upgraded = 0;
      this.resetVisible = false
    },
    upgradeResource: function() {
    	if(this.canUpgrade){
      	this.resources[0].quantity -= this.resources[1].cost;
        this.resources[1].upgrade ++;
        this.resources[1].cost *= 2;
        this.resources[1].upgraded ++;
      }
    },
    shorten: function(number) {
      var magnitude = Math.floor(Math.log10(number)),
          comma = Math.floor(magnitude/3),
          divisor = Math.pow(1000, comma),
      		short = Number.parseFloat(number/divisor).toPrecision(3),
          suffix = this.numberSuffix[comma-1]

      if ( number < 1000 ) {
        return number
      }

      return short + suffix;
    },
    buttonDisabled: function(quantity) {
      const cost = this.staffs[this.activeStaff].cost,
      			money = this.resources[0].quantity,
            positions = this.resources[1].quantity,
            supply = (this.activeStaff > 0) ? this.staffs[this.activeStaff - 1].quantity : positions * cost

      return (quantity > positions || quantity * cost > supply || quantity * cost > money)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  background: #000;
  padding: 20px;
  font-family: Helvetica;
}

#app {
  max-width: 504px;
  background-image: url('https://peter.gedeon.name/files/2018/07/exploding-world.jpg');
  transition: all 0.2s;
  margin: auto;
}

#blurrr  {
  background: rgba(164, 204, 255, 0.75);
  padding: 20px;
  border-radius: 4px;
  border:2px solid black;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

.smallText {
  font-size: x-small;
}

#reset {
  color: darkred;
  border-color: darkred;
  border-radius: 5px;
  float: right;
}

#help {
  float: right;
  border-radius: 10px;
}

#money {
  float: left;
  width: 48%;
}

#positions {
  float: right;
  width: 48%;
}

#copyright{
  float: none;
}

.overlay {
  background-color: lightgreen;
  position: absolute;
  top: 30px;
  padding: 10%;
  display: none;
  text-align: center;
  width: 80%;
  left: 0;
}

.visible {
  display: block;
}

#anker {
  clear: both;
  width: 100%;
}
</style>
