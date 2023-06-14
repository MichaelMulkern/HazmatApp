<template>
    <div id="main-area">
        <h2>Transporting Lithium Batteries by Air</h2>
        <p id="top-paragraph">All cells and batteries must be tested in accordance
            with the UN Manual of Tests and Criteria Part III
            Subsection 38.3 (DGR 3.9.2.6)</p>
        <hr>

        <div id="battery-type">
            <h3>Type of battery begin transported:</h3>
            <select v-on:change="handleType()" class="dropdown" v-model="lithiumIon">
                <option class="drop-option" value="true">Lithium Ion</option>
                <option class="drop-option" value="false">Lithium Metal</option>
            </select>

        </div>

        <div id="packed-type" v-show="showType">
            <h3>How are the batteries packed?</h3>
            <select v-on:change="handlePacked()" class="dropdown" v-model="howPacked">
                <option class="drop-option" value="contained">Contained Within Equipment</option>
                <option class="drop-option" value="separate">Packed Alongside Equipment</option>
                <option class="drop-option" value="loose">Stand Alone</option>
            </select>
        </div>

        <div id="use-intl" v-show="showUsa">
            <h3>Will this shipment travel to, from, or within the the USA?</h3>
            <select v-on:change="handleUsaOrIntl()" class="dropdown" v-model="usaOrIntl">
                <option class="drop-option" value="usa">Yes</option>
                <option class="drop-option" value="international">No</option>
            </select>
        </div>

        <div id="batt-cell" v-show="showBattOrCell">
            <h3>Are you shipping cells or batteries?</h3>
            <select v-on:change="handleCellsOrBatteries()" class="dropdown" v-model="battOrCell">
                <option class="drop-option" value="battery">Batteries</option>
                <option class="drop-option" value="cell">Cells</option>
            </select>
        </div>
<!--DIVERGE HERE-->
<div id="batt-cell" v-show="showBattOrCell">
            <h3>Are you shipping cells or batteries?</h3>
            <select v-on:change="handleCellsOrBatteries()" class="dropdown" v-model="battOrCell">
                <option class="drop-option" value="battery">Batteries</option>
                <option class="drop-option" value="cell">Cells</option>
            </select>
        </div>
<!--WEIGHT STUFF need to add WH as well-->
        <div id="watt-hours">
            
        </div>
        <div id="li-content" v-show="showWeight">
            <h3>What is the weight of lithium content in grams (g) per cell?</h3>
            <label for="weight">Grams:</label>
            <input type="number" id="weight" name="weight" v-model="weightOfLi">
        </div>

        <div id="package-weight" v-show="weightOfLi">
            <h3>What is the net quantity in kilograms (KG) of cells per package?</h3>
            <label for="pkg-weight">Kilograms:</label>
            <input type="number" id="pkg-weight" name="pkg-weight" v-model="packageWeight">
        </div>
        <!--REPORT BELOW THIS LINE-->


        <div id="report-preview" v-show="packageWeight">
            <hr>
            <h2>Report Preview</h2>
            <button>CREATE PDF (NON FUNCTIONAL)</button>
            <div id="top-header">
                <h3 class="top-boxes">{{ showIonOrMetal }}</h3>
                <h3 class="top-boxes">Regulated info</h3>
                <h3 class="top-boxes">UN Reference</h3>
                <h3 class="top-boxes">{{ showHowPackaged }}</h3>

            </div>
            <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et
                dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip
                ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu
                fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia
                deserunt mollit anim id est laborum.</p>
        </div>
    </div>
</template>

<script>
export default {
    name: "AirTransport",
    data() {
        return {
            howPacked: "",
            passedUn: true,
            isIon: false,
            isMetal: false,
            showWarning: false, //TODO make a warning for not passed UN
            containedIn: false,
            packedWith: false,
            packedAlone: false,
            lithiumIon: "",
            weightOfLi: 0,
            showType: false,
            showWeight: false,
            showPackageWeight: false,
            packageWeight: 0,
            showUsa: false,
            usaOrIntl: "",
            showBattOrCell: false,
            battOrCell: ""
        }
    },
    methods: {
        //Not currently used
        handlePassed() {
            if (this.passedUn == "true") {
                this.showType = true;
            } else {
                this.showType = false;
            }
        },
        handlePacked() {
            switch (this.howPacked) {
                case "contained":
                    this.containedIn = true
                    this.packedWith = false
                    this.packedAlone = false
                    this.showUsa = true
                    break;
                case "separate":
                    this.containedIn = false
                    this.packedWith = true
                    this.packedAlone = false
                    this.showUsa = true
                    break;
                case "loose":
                    this.containedIn = false
                    this.packedWith = false
                    this.packedAlone = true
                    this.showUsa = true
                    break;
            }
        },
        handleType() {
            switch (this.lithiumIon) {
                case "true":
                    this.isIon = true
                    this.isMetal = false
                    this.showType = true
                    break;
                case "false":
                    this.isIon = false
                    this.isMetal = true
                    this.showType = true
                    break;
            }
        },
        handleUsaOrIntl(){
            this.showBattOrCell = true;
        },  
        handleCellsOrBatteries(){
            if(this.packedWith){
                this.showWeight = true;
            }
            this.showWeight = true;
        },
    },
    computed: {
        showIonOrMetal() {
            if (this.isIon == true) {
                return "Lithium Ion";
            } else if (this.isMetal == true) {
                return "Lithium Metal";
            } else {
                return "Undefined";
            }
        },

        //THIS SECTION FOR AIR TRANSPORT ONLY CHANGE FPR OTHER MODES OF TRANSPORT
        //FIX THIS AFTER ADDING MORE INFO
        //showRegulated() {
        //    if (this.isMetal == true) {
        //        if (1 == true) {
        //            return "y";
        //        }
        //    }
        //    return "x";
        //},
        showHowPackaged() {
            if (this.containedIn == true) {
                return "Contained In";
            } else if (this.packedWith == true) {
                return "Packed With";
            } else if (this.packedAlone == true) {
                return "Stand Alone";
            } else {
                return "Undefined";
            }

        },
    }
}
</script>

<style scoped>
#top-paragraph {
    color: red;
}

#top-header {
    display: flex;
    flex-direction: row;
    justify-content: center;

}

.top-boxes {
    padding-left: 30px;
    padding-right: 30px;
    padding-top: 5px;
    padding-bottom: 5px;
    border: 2px solid black;
    font-size: 30px;
}
</style>