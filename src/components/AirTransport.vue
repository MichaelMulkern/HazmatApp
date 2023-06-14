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

        <div id="battery-type" v-show="showType">
            <h3>How are the batteries packed?</h3>
            <select v-on:change="handlePacked()" class="dropdown" v-model="howPacked">
                <option class="drop-option" value="contained">Contained Within Equipment</option>
                <option class="drop-option" value="separate">Packed Alongside Equipment</option>
                <option class="drop-option" value="loose">Stand Alone</option>
            </select>
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
            packageWeight: 0
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
                    this.showWeight = true
                    break;
                case "separate":
                    this.containedIn = false
                    this.packedWith = true
                    this.packedAlone = false
                    this.showWeight = true
                    break;
                case "loose":
                    this.containedIn = false
                    this.packedWith = false
                    this.packedAlone = true
                    this.showWeight = true
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
        }
    }
}
</script>

<style scoped>
#top-paragraph {
    color: red;
}
</style>