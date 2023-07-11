<template>
    <div id="main-area">
        <h2 v-if="showStart">Transporting Lithium Batteries by {{ transport }}</h2>

        <div id="transport-mode" class="selection-block" v-if="showTransport">
            <span>Select mode of transport: </span>
            <select name="transport-list" class="dropdown" v-model="transport">
                <option class="drop-option" value="Air">Air</option>
                <option class="drop-option" value="Ocean">Maritime</option>
                <option class="drop-option" value="Ground">49 CFR Road/Rail</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleModeOfTransport()">BEGIN</button>
            </div>
        </div>


        <div id="battery-type" class="selection-block" v-if="showMetalOrIon">
            <h3>Type of battery being transported:</h3>
            <select class="dropdown" v-model="lithiumIon">
                <option class="drop-option" value="true">Lithium Ion</option>
                <option class="drop-option" value="false">Lithium Metal</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(0)">BACK</button><button class="nav-buttons"
                    v-on:click="handleType()">NEXT</button>
            </div>
        </div>
        <div id="packed-type" class="selection-block" v-if="showHowPacked">
            <h3>How are the batteries packed?</h3>
            <select class="dropdown" v-model="howPacked">
                <option class="drop-option" value="contained">Contained Within Equipment</option>
                <option class="drop-option" value="separate">Packed Alongside Equipment</option>
                <option class="drop-option" value="loose">Stand Alone</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(1)">BACK</button><button class="nav-buttons"
                    v-on:click="handlePacked()">NEXT</button>
            </div>
        </div>
        <div id="use-intl" class="selection-block" v-if="showUsa">
            <h3>Will this shipment travel to, from, or within the the USA?</h3>
            <select class="dropdown" v-model="usaOrIntl">
                <option class="drop-option" value="usa">Yes</option>
                <option class="drop-option" value="international">No</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(2)">BACK</button><button class="nav-buttons"
                    v-on:click="handleUsaOrIntl()">NEXT</button>
            </div>
        </div>
        <div id="batt-cell" class="selection-block" v-if="showBattOrCell">
            <h3>Are you shipping cells or batteries?</h3>
            <select class="dropdown" v-model="battOrCell">
                <option class="drop-option" value="battery">Batteries</option>
                <option class="drop-option" value="cell">Cells</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(3)">BACK</button><button class="nav-buttons"
                    v-on:click="handleCellsOrBatteries()">NEXT</button>
            </div>
        </div>

        <!--WEIGHT STUFF HERE-->
        <div id="watt-hours" class="selection-block" v-if="showWh && isIon">
            <h3 class="question-header">What is the watt-hour (WH) rating per {{ battOrCell }}?</h3>
            <label for="watt-hour">WH:</label>
            <input type="number" id="watt-hour" name="watt-hour" v-model="wattHour">
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(4)">BACK</button><button class="nav-buttons"
                    v-on:click="handleShowPkgWeight()">NEXT</button>
            </div>
        </div>

        <div id="li-content" class="selection-block" v-if="showWeight && isMetal">
            <h3 class="question-header">What is the weight of lithium content in grams (g) per {{ battOrCell }}?</h3>
            <label for="weight">Grams:</label>
            <input type="number" id="weight" name="weight" v-model="weightOfLi">
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(4)">BACK</button><button class="nav-buttons"
                    v-on:click="handleShowPkgWeight()">NEXT</button>
            </div>
        </div>

        <div id="package-weight" class="selection-block" v-if="showPackageWeight">
            <h3>What is the net quantity in kilograms (KG) of {{ battOrCell }} per package?</h3>
            <label for="pkg-weight">Kilograms:</label>
            <input type="number" id="pkg-weight" name="pkg-weight" v-model="packageWeight">
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(5)">BACK</button><button class="nav-buttons"
                    v-on:click="handlePackageJunction()">NEXT</button>
            </div>
        </div>
        <!--PROMPTS BELOW THE WEIGHTS-->

        <div id="grams-question" class="selection-block" v-if="showGramsQuestion">
            <h3>Does the quantity of lithium metal in the equipment exceed {{ gramsOption }}?</h3>
            <select class="dropdown" v-model="gramsAnswer">
                <option class="drop-option" value="true">Yes</option>
                <option class="drop-option" value="false">No</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleGramsQuestion()">NEXT</button>
            </div>
        </div>

        <div id="a-set-question" class="selection-block" v-if="showASetQuestion">
            <h3 class="question-header">How many {{ battOrCell }} does the package include? Note: A "set" of {{ battOrCell
            }} is the
                number of individual {{ battOrCell }} that are required to power each piece of equipment.</h3>
            <select class="dropdown" v-model="aSetAnswer">
                <option class="drop-option" value="less">The minimum number of {{ battOrCell }} required for the equipment's
                    operations, plus no more than 2 spare sets.</option>
                <option class="drop-option" value="more">The minimum number of {{ battOrCell }} required for the equipment's
                    operations, plus more than 2 spare sets.</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleASetQuestion()">NEXT</button>
            </div>
        </div>

        <div id="two-batt-rail" class="selection-block" v-if="showTwoBattAndButton">
            <h3>Does the package contain more than 2 batteries installed in the equipment?</h3>
            <select class="dropdown" v-model="twoBattAndButton">
                <option class="drop-option" value="more">Contains more than 2 batteries</option>
                <option class="drop-option" value="less">Contains 2 batteries or less</option>
                <option class="drop-option" value="button">Contains button cell batteries (includes circuit boards)</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleTwoBattRailOnly()">NEXT</button>
            </div>
        </div>

        <div id="two-batt" class="selection-block" v-if="showTwoBattOnly">
            <h3>Does the package contain more than 2 batteries installed in the equipment?</h3>
            <select class="dropdown" v-model="twoBattOnly">
                <option class="drop-option" value="true">Contains more than 2 batteries</option>
                <option class="drop-option" value="false">Contains 2 batteries or less</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleTwoBatt()">NEXT</button>
            </div>
        </div>

        <div id="contained-in-consignment" class="selection-block" v-if="showConsignment">
            <h3 class="question-header">How many of these packages are contained within your consignment?</h3>
            <select class="dropdown" v-model="amountInConsignment">
                <option class="drop-option" value="true">Consignment contains no more than two such packages</option>
                <option class="drop-option" value="false">Consignment contains more than two such packages</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(9)">BACK</button><button class="nav-buttons"
                    v-on:click="handleNumberContainedInConsignment()">NEXT</button>
            </div>
        </div>

        <div id="section-two-consignment" class="selection-block" v-if="showSectionTwoConsignment">
            <h3>How many of these packages are contained within your consignment?</h3>
            <select class="dropdown" v-model="sectionTwoConsignment">
                <option class="drop-option" value="false">Consignment contains no more than two such Section II packages
                </option>
                <option class="drop-option" value="true">Consignment contains more than two such Section II packages
                </option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(8)">BACK</button><button class="nav-buttons"
                    v-on:click="handleSectionTwoPackages()">NEXT</button>
            </div>
        </div>

        <div id="more-than-needed" class="selection-block" v-if="showMoreThanNeeded">
            <h3 class="question-header">Does the package contain more than the number of cells necessary to power the piece
                of equipment?</h3>
            <select class="dropdown" v-model="moreThanNeeded">
                <option class="drop-option" value="true">Yes</option>
                <option class="drop-option" value="false">No</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(7)">BACK</button><button class="nav-buttons"
                    v-on:click="handleMoreThanNeededToPower()">NEXT</button>
            </div>
        </div>


        <div id="four-or-more" class="selection-block" v-if="showFourBattAndButton">
            <h3 class="question-header">Does the package contain more than 4 cells or button cells installed in the
                equipment?</h3>
            <select class="dropdown" v-model="fourBattAndButton">
                <option class="drop-option" value="more">Contains more than 4 cells</option>
                <option class="drop-option" value="less">Contains 4 cells or less</option>
                <option class="drop-option" value="button">Contains button cells (includes circuit boards)</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleMoreThanFourCells()">NEXT</button>
            </div>
        </div>

        <div id="four-or-more-rail-only" class="selection-block" v-if="showFourBattOnly">
            <h3 class="question-header">Does the package contain more than 4 cells installed in the equipment?</h3>
            <select class="dropdown" v-model="fourBattOnly">
                <option class="drop-option" value="more">Contains more than 4 cells</option>
                <option class="drop-option" value="less">Contains 4 cells or less</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(5)">BACK</button><button class="nav-buttons"
                    v-on:click="handleMoreThanFourRailOnly()">NEXT</button>
            </div>
        </div>

        <div id="state-of-charge" class="selection-block" v-if="showStateOfCharge">
            <h3 class="question-header">What is the state of charge (SoC) % of the {{ battOrCell }} being shipped?</h3>
            <label for="charge-state">State of Charge:</label>
            <input type="number" id="charge-state" name="charge-state" v-model="stateOfCharge">
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleStateOfCharge()">NEXT</button>
            </div>
        </div>


        <div id="batts-in-package" class="selection-block" v-if="showBattsInPkg">
            <h3 class="question-header">How many {{ battOrCell }} does the package include? Note: A "set" of {{
                battOrCell }} is the
                number of individual {{ battOrCell }} that are required to power each piece of equipment.</h3>
            <select class="dropdown" v-model="battsInPkg">
                <option class="drop-option" value="true">The minimum number of {{ battOrCell }} required for the
                    equipment's
                    operations, plus no more than 2 spare sets.</option>
                <option class="drop-option" value="false">The minimum number of {{ battOrCell }} required for the
                    equipment's
                    operations, plus more than 2 spare sets</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleNumberOfBattsInPackage()">NEXT</button>
            </div>
        </div>
        <!--MODALS HERE  -->
        <div class="warning-modal">
            <warningModal :open="isOpen" @close="isOpen = !isOpen" id="warning-box">

                <h2 class="bold-white" v-show="socWarning">Lithium ion cells and batteries at a state of charge (SoC)
                    exceeding 30% of their
                    rated capacity may only be shipped with the approval of the State of Origin and the State of the
                    Operator (see Special Provision A331).</h2>
                <h2 class="bold-white" v-show="moreThanTwoWarning">This package must be repacked accordingly so there are no
                    more than two spare
                    sets per each piece of equipment contained within the package or must be approved by the appropriate
                    competent authorities.</h2>
                <h2 class="bold-white" v-show="moreThanNeededWarning">Packages containing a combination of lithium batteries
                    contained in and lithium batteries packed with equipment must be marked as "Packed with Equipment" and
                    also meet the requirements of Special Provision A181. Please reproduce this shipment accordingly.</h2>
                <h2 class="bold-white" v-show="aSetWarning">This package must be repacked accordingly so there are
                    no more than two spare sets per each piece of equipment contained within the package or must be approved
                    by the appropriate competent authorities.</h2>
                <h2 class="bold-white" v-show="gramsWarning">The international air regulations limit the quantity of lithium
                    metal in any piece of equipment to a maximum of 12 g per cell and 500 g per battery. This package must
                    be repacked accordingly or must be approved by the appropriate competent authorities.</h2>
                <h2 class="bold-white" v-show="thirtyFiveKiloWarning">This package must be repacked accordingly so the net
                    quantity does not exceed 35 kg; or, per Special Provision A99, a lithium cell or battery meeting the
                    requirements of Section I of the applicable packing instruction may have a mass exceeding 35 kg with the
                    approval of the appropriate authority of the State of Origin and the State of the operator and the
                    requirements in Packing Instruction 974 of the ICAO Supplement to the Technical Instructions are met.
                </h2>
            </warningModal>
        </div>
        <!--REPORT BELOW THIS LINE-->

        <div>
            <reportModal :open="openReport" @close="openReport = !openReport" id="report-box">
                <h3>REPORT IS READY TO VIEW</h3>
                <a id="show-report" class="report-button" v-if="showReportButton" v-on:click="handleShowReport()"
                    v-bind:href="reportLink" target="_blank">OPEN PDF</a>
            </reportModal>
        </div>

    </div>
</template>

<script>
import warningModal from "@/components/WarningModal.vue";
import reportModal from "@/components/ReportModal.vue";
import { ref } from "vue";
export default {
    name: "BatteryTree",
    components: { warningModal, reportModal },
    setup() {
        const isOpen = ref(false)
        return { isOpen }
    },
    data() {
        return {
            transport: "",
            howPacked: "",
            showMetalOrIon: false,
            showStart: false,
            isIon: false,
            isMetal: false,
            showWarning: false, //TODO make a warning for not passed UN
            lithiumIon: "",
            weightOfLi: 0,
            showHowPacked: false,
            showWeight: false,
            showPackageWeight: false,
            packageWeight: 0,
            showUsa: false,
            usaOrIntl: "",
            showBattOrCell: false,
            battOrCell: "",
            showTwoBattOnly: false,
            twoBattOnly: "",
            wattHour: 0,
            showWh: false,
            showReport: false,
            showPkgWeight: false,
            reportButton: "Show Report Preview",
            battsInPkg: "",
            showBattsInPkg: false,
            showFourBattAndButton: false,
            fourOrMore: "",
            showMoreThanNeeded: false,
            moreThanNeeded: "",
            showConsignment: false,
            amountInConsignment: "",
            showTwoBattsInstalled: false,
            twoBattsInstalled: "",
            showStateOfCharge: false,
            stateOfCharge: 0,
            showSectionTwoConsignment: false,
            sectionTwoConsignment: "",
            showBelowWeightOptions: true,
            showReportButton: true,
            previousMenu: "",
            showTransport: true,
            socWarning: false,
            moreThanTwoWarning: false,
            fourBattAndButton: "",
            moreThanNeededWarning: false,
            showTwoBattAndButton: false,
            twoBattAndButton: "",
            showASetQuestion: false,
            aSetAnswer: "",
            aSetWarning: false,
            gramsOption: "",
            showGramsQuestion: false,
            gramsAnswer: "",
            gramsWarning: false,
            fourBattOnly: "",
            showFourBattOnly: false,
            thirtyFiveKiloWarning: false,
            openReport: false,
            reportLink: "",

        }
    },
    methods: {
        handleModeOfTransport() {
            this.showMetalOrIon = true;
            this.showTransport = false;
        },
        handleBack(menuNumber) {
            switch (menuNumber) {
                case 0:
                    this.showMetalOrIon = false
                    this.showTransport = true
                    break;
                case 1:
                    this.showMetalOrIon = true
                    this.showHowPacked = false
                    break;
                case 2:
                    this.showHowPacked = true
                    this.showUsa = false
                    break;
                case 3:
                    if (this.transport == "Ground") {
                        this.showBattOrCell = false
                        this.showHowPacked = true;
                    } else {
                        this.showUsa = true
                        this.showBattOrCell = false
                    }
                    break;
                case 4:
                    this.showWh = false
                    this.showWeight = false
                    this.showBattOrCell = true
                    break;
                case 5:
                    if (this.isIon) {
                        this.showWh = true;
                        this.showPackageWeight = false;
                        
                    } else if (this.isMetal) {
                        this.showWeight = true;
                        this.showPackageWeight = false;
                        this.showFourBattOnly = false;
                    }
                    break;
                case 6:
                    if (this.transport == "Ground" && this.howPacked == "contained") {
                        this.showFourBattAndButton = false;
                        this.showBattsInPkg = false;
                        this.showStateOfCharge = false;
                        this.showTwoBattOnly = false;
                        this.showTwoBattAndButton = false;
                        this.showASetQuestion = false;
                        this.showGramsQuestion = false;
                        this.showPackageWeight = true;
                        //this.showWh = true; Wy was this here??
                    } else {
                        this.showFourBattAndButton = false;
                        this.showBattsInPkg = false;
                        this.showStateOfCharge = false;
                        this.showTwoBattOnly = false;
                        this.showTwoBattAndButton = false;
                        this.showASetQuestion = false;
                        this.showGramsQuestion = false;
                        this.showPackageWeight = true;
                    }
                    break;
                case 7:
                    this.showMoreThanNeeded = false;
                    this.showGramsQuestion = false;
                    if (this.battOrCell == "battery" && this.isIon) {
                        this.showTwoBattOnly = true;
                    } else if (this.battOrCell == "cell" && this.isIon) {
                        this.showFourBattAndButton = true;
                    } else if (this.transport == "Air" && this.isMetal) {
                        this.showGramsQuestion = true;
                    }
                    break;
                case 8:
                    this.showSectionTwoConsignment = false;
                    this.showMoreThanNeeded = true;
                    break;
                case 9:
                    if (this.isIon) {
                        if (this.battOrCell == "battery") {
                            if(this.transport == "Ground"){
                            this.showTwoBattAndButton = true;
                            this.showConsignment = false;
                            }else{
                            this.showTwoBattOnly = true;
                            this.showConsignment = false;
                            }
                        } else if (this.battOrCell == "cell") {
                            this.showFourBattAndButton = true;
                            this.showConsignment = false;
                        }
                    } else if (this.isMetal) {
                        if (this.battOrCell == "cell") {
                            if(this.transport == "Ground"){
                            this.showFourBattAndButton = true;
                            this.showConsignment = false;
                            }else{
                            this.showFourBattOnly = true;
                            this.showConsignment = false;
                            }
                        } else if (this.battOrCell == "battery") {
                            if(this.transport == "Ground"){
                            this.showTwoBattAndButton = true;
                            this.showConsignment = false;
                            }else{
                            this.showTwoBattOnly = true;
                            this.showConsignment = false;
                            }
                        }
                    }
                break;
            }

        },
        handlePacked() {
            if (this.transport == "Ground") {
                this.showBattOrCell = true;
                this.showHowPacked = false;
            } else {
                this.showUsa = true;
                this.showHowPacked = false;
            }
        },
        handleType() {
            switch (this.lithiumIon) {
                case "true":
                    this.isIon = true
                    this.isMetal = false
                    this.showHowPacked = true
                    this.showMetalOrIon = false
                    break;
                case "false":
                    this.isIon = false
                    this.isMetal = true
                    this.showHowPacked = true
                    this.showMetalOrIon = false
                    break;
            }

        },
        handleUsaOrIntl() {
            this.showBattOrCell = true;
            this.showUsa = false
        },
        handleCellsOrBatteries() {
            if (this.isIon) {
                this.showWh = true;
                this.showWeight = false;
                this.showBattOrCell = false;

            } else if (this.isMetal) {
                this.showWeight = true;
                this.showWh = false;
                this.showBattOrCell = false;

            }
        },
        //Also has some rail only stuff that skips the package weight
        handleShowPkgWeight() {
            if (this.transport == "Ocean" && this.maritimeLimiter) {
                this.openReport = true;
            } else if (this.transport == "Ground" && ((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "contained") {
                this.showFourBattOnly = true;
                this.showWh = false;
            } else if (this.transport == "Ground" && this.groundLimiter && (this.howPacked == "separate" || this.howPacked == "contained")) {
                this.openReport = true;
            } else if (this.transport == "Ground" && this.isMetal && this.howPacked == "contained" && this.battOrCell == "cell"  && this.weightOfLi > 1) {
                this.showFourBattOnly = true;
                this.showWeight = false;
            } else if (this.transport == "Ground" && this.isMetal && this.howPacked == "contained" && this.battOrCell == "battery" && this.weightOfLi > 2) {
                this.showTwoBattOnly = true;
                this.showWeight = false;
            }else if (this.transport == "Ground" && this.isMetal && this.howPacked == "loose" && ((this.battOrCell == "cell" && this.weightOfLi > 5) || (this.battOrCell == "battery" && this.weightOfLi > 25))){
                this.openReport = true;
            }
            else {
                this.showPackageWeight = true;
                this.showWeight = false;
                this.showWh = false;
            }
        },
        //==============REPORT SECTION========================================
        handleShowReport() {
            this.reportLink = "#";
            //AIR REPORTS
            if(this.transport == "Air" && this.isIon){
                //Loose batteries
                if(((this.wattHour > 20 && this.battOrCell == "cell") || (this.wattHour > 100 && this.battOrCell == "battery")) && this.packageWeight <= 35 && this.howPacked == "loose"){
                    this.reportLink = "/files/IonAir/IATA.US.PI965.IA.pdf";
                }else if(((this.wattHour <= 20 && this.battOrCell == "cell") || (this.wattHour <= 100 && this.battOrCell == "battery")) && this.howPacked == "loose"){
                    if(this.packageWeight > 10){
                        this.reportLink = "/files/IonAir/IATA.US.PI965.Net%20Qty_IA.pdf";
                    }else{
                    this.reportLink = "/files/IonAir/IATA.US.PI965.IB.pdf";
                    }
                //Packed With
                }else if(((this.wattHour <= 20 && this.battOrCell == "cell") || (this.wattHour <= 100 && this.battOrCell == "battery")) && this.howPacked == "separate"){
                    this.reportLink = this.packageWeight > 5 ? "/files/IonAir/IATA.US.PI966.Net%20Qty_I.CAO.pdf" : "/files/IonAir/IATA.US.PI966.II.pdf";
                }else if(((this.wattHour > 20 && this.battOrCell == "cell") || (this.wattHour > 100 && this.battOrCell == "battery")) && this.howPacked == "separate"){
                    this.reportLink = this.packageWeight > 5 ? "/files/IonAir/IATA.US.PI966.I.CAO.pdf" : "/files/IonAir/IATA.US.PI966.I.PAX.pdf";
                //Contained In
                }else if(((this.wattHour > 20 && this.battOrCell == "cell") || (this.wattHour > 100 && this.battOrCell == "battery")) && this.howPacked == "contained"){
                    this.reportLink = this.packageWeight > 5 ? "/files/IonAir/IATA.US.PI967.I.CAO.pdf" : "/files/IonAir/IATA.US.PI967.I.PAX.pdf";
                }else if(((this.wattHour <= 20 && this.battOrCell == "cell") || (this.wattHour <= 100 && this.battOrCell == "battery")) && this.howPacked == "contained"){
                    if(this.wattHour > 5){
                        this.reportLink = "/files/IonAir/IATA.US.PI967.Net%20Qty_I.CAO.pdf";
                    }else if (this.twoBattOnly == "true" || this.fourBattAndButton == "more"){
                        this.reportLink = "/files/IonAir/IATA.US.PI967.II.More%204cell_2bat.pdf";
                    }else if (this.twoBattOnly == "false" || this.fourBattAndButton == "less"){
                        this.reportLink = this.sectionTwoConsignment == "true" ? "/files/IonAir/IATA.US.PI967.II.More%202%20Pkg.pdf" : "/files/IonAir/IATA.US.PI967.II.2%20Pkg.pdf";
                    }else if (this.fourBattAndButton == "button"){
                        this.reportLink = "/files/IonAir/IATA.US.PI967.II.BCell.pdf"; //Why international only?? 
                    }
                }
            }else if (this.transport == "Air" && this.isMetal){
                //Loose packed
                if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "loose" ){
                    this.reportLink = "/files/MetalAir/IATA.US.PI968.IA.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "loose" ){
                    this.reportLink = this.packageWeight > 2.5 ? "/files/MetalAir/IATA.US.PI968_IA_CAO.pdf" : "/files/MetalAir/IATA.US.PI968.IB.pdf";
                //Packed With
                }else if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "separate" ){
                    this.reportLink = this.packageWeight > 5 ? "/files/MetalAir/IATA.US.PI969.I.CAO.pdf" : "/files/MetalAir/IATA.US.PI969.I.PAX.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "separate" ){
                    this.reportLink = this.packageWeight > 5 ? "/files/MetalAir/IATA.US.PI969.NetQty_I.CAO.pdf" : "/files/MetalAir/IATA.US.PI969.II.pdf";
                //Contained inside
                }else if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "contained" ){
                    this.reportLink = this.packageWeight > 5 ? "/files/MetalAir/IATA.US.PI970.I.CAO.pdf" : "/files/MetalAir/IATA.US.PI970.I.PAX.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "contained" && this.packageWeight <= 5 && this.fourBattAndButton != "button"){
                    if (this.twoBattOnly == "true" || this.fourBattAndButton == "more"){
                        this.reportLink = "/files/MetalAir/IATA.US.PI970.II.pdf";
                    }else{
                        this.reportLink = "/files/MetalAir/IATA.US.PI970.II.%20excepted%20Pkg.pdf";
                    }
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "contained" && this.packageWeight > 5){
                    this.reportLink = "/files/MetalAir/IATA.US.PI970.Net%20Qty_I.CAO.pdf"
                }else if(this.fourBattAndButton == "button"){
                    this.reportLink = "/files/MetalAir/IATA.US.PI970.II.BCell.pdf";
                }
            //GROUND REPORTS
            }else if(this.transport == "Ground" && this.isIon){
                //Stand Alone ION
                if(((this.battOrCell == "cell" && this.wattHour > 60) || (this.battOrCell == "battery" && this.wattHour > 300)) && this.howPacked == "loose"){
                    this.reportLink = "/files/IonGround/49CFR.3480.C9.Large.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "loose"){
                    this.reportLink = this.packageWeight > 30 ? "/files/IonGround/49CFR.3480.C9.Med.pdf" : "/files/IonGround/49CFR.3480.Ex.Med.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.howPacked == "loose"){
                    this.reportLink = this.packageWeight > 30 ? "/files/IonGround/49CFR.3480.C9.Small.pdf" : "/files/IonGround/49CFR.3480.Ex.Small.pdf";
                //Contained in ION
                } else if(((this.battOrCell == "cell" && this.wattHour > 60) || (this.battOrCell == "battery" && this.wattHour > 300)) && this.howPacked == "contained"){
                    this.reportLink = "/files/IonGround/49CFR.3481.Cont.Large.pdf";
                } else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.packageWeight <= 5 && this.howPacked == "contained"){
                    if(this.twoBattAndButton == "button" || this.fourBattAndButton == "button"){
                        this.reportLink = "/files/IonGround/49CFR.3481.Cont.Ex.BC.pdf";
                    }else if (this.fourBattAndButton == "more" || this.twoBattAndButton == "more"){
                        this.reportLink = "/files/IonGround/49CFR.3481.Cont.Ex.Small.more4cell_2bat.pdf";
                    }else if (this.fourBattAndButton == "less" || this.twoBattAndButton == "less"){
                        this.reportLink = this.amountInConsignment == "true" ? "/files/IonGround/49CFR.3481.Cont.Ex.Small.excepted%20pkg.pdf" : "/files/IonGround/49CFR.3481.Cont.Ex.Small.more2pkg.pdf";      
                    }
                } else if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "contained"){
                    if(this.fourBattAndButton == "more" || this.fourBattOnly == "more"){
                        this.reportLink = "/files/IonGround/49CFR.3481.Cont.Ex.Med.more4cell_2bat.pdf";
                    }else if (this.fourBattAndButton == "less" || this.fourBattOnly == "less"){
                        this.reportLink = this.amountInConsignment == "true" ? "/files/IonGround/49CFR.3481.Cont.Ex.Med.2pkg.pdf" : "/files/IonGround/49CFR.3481.Cont.Ex.Med.more2pkg.pdf";
                    }
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.packageWeight > 5 && this.howPacked == "contained"){
                    if (this.fourBattAndButton == "button" || this.twoBattAndButton == "button"){
                        this.reportLink = "/files/IonGround/49CFR.3481.Cont.Ex.BC.CAO.pdf";
                    }else if (this.fourBattAndButton == "more" || this.twoBattAndButton == "more") {
                        this.reportLink = "/files/IonGround/49CFR.3481.Cont.Ex.Small.more4cell_2bat.CAO.pdf";
                    }else if (this.fourBattAndButton == "less" || this.twoBattAndButton == "less") {
                        this.reportLink = this.amountInConsignment == "true" ? "/files/IonGround/49CFR.3481.Cont.Ex.Small.2pkg.CAO.pdf" : "/files/IonGround/49CFR.3481.Cont.Ex.Small.more2pkg.CAO.pdf";
                    }
                // Packed with ION
                }else if(((this.battOrCell == "cell" && this.wattHour > 60) || (this.battOrCell == "battery" && this.wattHour > 300)) && this.howPacked == "separate"){
                    this.reportLink = "/files/IonGround/49CFR.3481.Pckd.Large.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "separate"){
                    this.reportLink = "/files/IonGround/49CFR.3481.Pckd.Ex.Med.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.howPacked == "separate"){
                    this.reportLink = this.packageWeight > 5 ? "/files/IonGround/49CFR.3481.Pckd.Ex.Small.CAO.pdf" : "/files/IonGround/49CFR.3481.Pckd.Ex.Small.pdf";        
                }
            }else if(this.transport == "Ground" && this.isMetal){
                //Stand alone Metal
                if(((this.battOrCell == "cell" && this.weightOfLi > 5) || (this.battOrCell == "battery" && this.weightOfLi > 25)) && this.howPacked == "loose"){
                    this.reportLink = "/files/MetalGround/49CFR.3090.C9.Large.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "loose"){
                    this.reportLink = this.packageWeight > 30 ? "/files/MetalGround/49CFR.3090.C9.Med.pdf" : "/files/MetalGround/49CFR.3090.Ex.Med.pdf";  
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "loose"){
                    this.reportLink = this.packageWeight > 30 ? "/files/MetalGround/49CFR.3090.C9.Small.pdf" : "/files/MetalGround/49CFR.3090.Ex.Small.pdf";  
                }
                //Contained in metal ground
                if(((this.battOrCell == "cell" && this.weightOfLi > 5) || (this.battOrCell == "battery" && this.weightOfLi > 25)) && this.howPacked == "contained"){
                    this.reportLink = "/files/MetalGround/49CFR.3091.Cont.Large.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "contained"){
                    if(this.twoBattOnly == "more" || this.fourBattOnly == "more"){
                        this.reportLink = "/files/MetalGround/49CFR.3091.Cont.Ex.Med.more4cell_2bat.pdf";
                    }else if (this.twoBattOnly == "less" || this.fourBattOnly == "less"){
                        this.reportLink = this.amountInConsignment == "true" ? "/files/MetalGround/49CFR.3091.Cont.Ex.Med.2pkg.pdf" : "/files/MetalGround/49CFR.3091.Cont.Ex.Med.more2pkg.pdf"; //Error with this report on battery count
                    }
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2))  && this.howPacked == "contained"){
                    if(this.twoBattAndButton == "more" || this.fourBattAndButton == "more"){
                        this.reportLink = this.packageWeight > 5 ? "/files/MetalGround/49CFR.3091.Cont.Ex.Small.more4cell_2bat.CAO.pdf" : "/files/MetalGround/49CFR.3091.Cont.Ex.Small.more4cell_2bat.pdf";
                    }else if(this.twoBattAndButton == "button" || this.fourBattAndButton == "button"){
                        this.reportLink = this.packageWeight > 5 ? "/files/MetalGround/49CFR.3091.Cont.Ex.BC.CAO.pdf" : "/files/MetalGround/49CFR.3091.Cont.Ex.BC.pdf";
                    } else if(this.twoBattAndButton == "less" || this.fourBattAndButton == "less"){
                        if(this.packageWeight > 5){
                            this.reportLink = this.amountInConsignment == "true" ? "/files/MetalGround/49CFR.3091.Cont.Ex.Small.2pkg.CAO.pdf" : "/files/MetalGround/49CFR.3091.Cont.Ex.Small.more2pkg.CAO.pdf";
                        }else if (this.packageWeight <= 5) {
                            this.reportLink = this.amountInConsignment == "true" ? "/files/MetalGround/49CFR.3091.Cont.Ex.Small.2pkg.pdf" : "/files/MetalGround/49CFR.3091.Cont.Ex.Small.more2pkg.pdf";
                        }
                    }
                }     
                //Packed with ground metal
                if(((this.battOrCell == "cell" && this.weightOfLi > 5) || (this.battOrCell == "battery" && this.weightOfLi > 25)) && this.howPacked == "separate"){
                    this.reportLink = "/files/MetalGround/49CFR.3091.Pckd.Large.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "separate"){
                    this.reportLink = "/files/MetalGround/49CFR.3091.Pckd.Ex.Med.pdf";
                }else if(((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.howPacked == "separate"){
                    this.reportLink = this.packageWeight > 5 ? "/files/MetalGround/49CFR.3091.Pckd.Ex.Small.CAO.pdf" : "/files/MetalGround/49CFR.3091.Pckd.Ex.Small.pdf";
                }
            // Ocean Ion
            }else if(this.transport == "Ocean" && this.isIon){
                // Stand Alone
                if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "loose"){
                    this.reportLink = "/files/IonOcean/IMO.US.3480.P903.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.howPacked == "loose"){
                    this.reportLink = this.packageWeight > 30 ? this.reportLink = "/files/IonOcean/IMO.US.3480.Pkg_wt_P903.pdf" : "/files/IonOcean/IMO.US.3480.SP188.pdf"; //Error in this report, never asks about number of cells in package
                // Contained
                }else if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "contained"){
                    this.reportLink = "/files/IonOcean/IMO.US.3481.Cont.P903.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.howPacked == "contained"){
                    if(this.fourBattAndButton == "more" || this.twoBattOnly == "true"){
                        this.reportLink = this.packageWeight > 5 ? this.reportLink = "/files/IonOcean/IMO.US.3481.Cont.SP188.more4cell_2bat.CAO.pdf" : "/files/IonOcean/IMO.US.3481.Cont.SP188.more4cell_2bat.pdf"; 
                    }else if ((this.fourBattAndButton == "less" || this.twoBattOnly == "false") && this.packageWeight > 5){
                        this.reportLink = this.amountInConsignment == "true" ? this.reportLink = "/files/IonOcean/IMO.US.3481.Cont.SP188.2pkg.CAO.pdf" : "/files/IonOcean/IMO.US.3481.Cont.SP188.more2pkg.CAO.pdf";
                    }else if ((this.fourBattAndButton == "less" || this.twoBattOnly == "false") && this.packageWeight <= 5){
                        this.reportLink = this.amountInConsignment == "true" ? this.reportLink = "/files/IonOcean/IMO.US.3481.Cont.SP188.2pkg.pdf" : "/files/IonOcean/IMO.US.3481.Cont.SP188.more2pkg.pdf";
                    }else if (this.fourBattAndButton == "button"){
                        this.reportLink = this.packageWeight > 5 ? this.reportLink = "/files/IonOcean/IMO.US.3481.Cont.SP188.BC.CAO.pdf" : "/files/IonOcean/IMO.US.3481.Cont.SP188.BC.pdf";
                    }
                // Packed with
                }else if(((this.battOrCell == "cell" && this.wattHour > 20) || (this.battOrCell == "battery" && this.wattHour > 100)) && this.howPacked == "separate"){
                    this.reportLink = "/files/IonOcean/IMO.US.3481.Pckd.P903.pdf";
                }else if(((this.battOrCell == "cell" && this.wattHour <= 20) || (this.battOrCell == "battery" && this.wattHour <= 100)) && this.howPacked == "separate") {
                    this.reportLink = this.packageWeight > 5 ? this.reportLink = "/files/IonOcean/IMO.US.3481.Pckd.SP188.CAO.pdf" : "/files/IonOcean/IMO.US.3481.Pckd.SP188.pdf"; 
                }

            }
        },
        //===============================================================================================
        handleMoreThanFourCells() {
            if (this.fourBattAndButton == "more") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.openReport = true;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showFourBattAndButton = false;
                }
            } else if (this.fourBattAndButton == "less") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showConsignment = true;
                    this.showFourBattAndButton = false;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showFourBattAndButton = false;
                }
            } else if (this.fourBattAndButton == "button") {
                this.openReport = true;
            }
        },
        handleMoreThanFourRailOnly() {
            if (this.fourBattOnly == "more") {
                this.openReport = true;
            } else if (this.fourBattOnly == "less") {
                this.showConsignment = true;
                this.showFourBattOnly = false;
            }
        },
        handleMoreThanNeededToPower() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            if (this.moreThanNeeded == "true") {
                this.moreThanNeededWarning = true;
                this.isOpen = true;
            } else if (this.moreThanNeeded == "false") {
                if (this.isIon && this.battOrCell == "cell" && this.transport == "Air") {
                    if (this.fourBattAndButton == "more" && this.wattHour <= 20) {
                        this.openReport = true;
                    } else if (this.fourBattAndButton == "less") {
                        this.showSectionTwoConsignment = true;
                        this.showMoreThanNeeded = false;
                    } else { this.openReport = true; }
                } else if (this.isMetal && this.transport == "Air") {
                    this.openReport = true;
                } else if (this.isIon && this.twoBattOnly == "true" && this.transport == "Air") {
                    this.openReport = true;
                } else if (this.isIon && this.twoBattOnly == "false" && this.transport == "Air") {
                    this.showSectionTwoConsignment = true;
                    this.showMoreThanNeeded = false;
                }
            }
        },
        handleNumberContainedInConsignment() {
            if (this.amountInConsignment == "true" || this.amountInConsignment == "false") {
                this.openReport = true;
            }
        },
        handleTwoBatt() {
            if (this.twoBattOnly == "true") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.openReport = true;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showTwoBattOnly = false;
                }
            } else if (this.twoBattOnly == "false") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showConsignment = true;
                    this.showTwoBattOnly = false;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showTwoBattOnly = false;
                }
            }
        },
        handleNumberOfBattsInPackage() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            if (this.battsInPkg == "false") {
                this.moreThanTwoWarning = true;
                this.isOpen = true;
            } else {
                this.openReport = true;
            }
        },
        handleStateOfCharge() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            //Air only max 30% charge 
            if (this.stateOfCharge > 30) {
                this.isOpen = true;
                this.socWarning = true;
            } else {
                this.openReport = true;
            }
        },
        handleSectionTwoPackages() {
            if (this.sectionTwoConsignment == "true" || this.sectionTwoConsignment == "false") {
                this.openReport = true;
            }
        },
        handlePackageJunction() {
            
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            if (this.transport == "Air" && this.packageWeight > 35) {
                this.thirtyFiveKiloWarning = true;
                this.isOpen = true;
            } else if (this.transport == "Air" && this.isIon && this.howPacked == "loose") {
                this.showStateOfCharge = true;
                this.showPackageWeight = false;
            } else if (this.transport == "Air" && this.howPacked == "separate") {
                this.showBattsInPkg = true;
                this.showPackageWeight = false;
            } else if (this.howPacked == "contained" && this.battOrCell == "cell" && this.isIon && this.wattHour <= 20) {
                //Transport doesn't matter
                this.showFourBattAndButton = true;
                this.showPackageWeight = false;
            } else if (this.howPacked == "contained" && ((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.transport == "Ground") { 
                if(this.battOrCell == "battery"){
                    this.showTwoBattAndButton = true;
                    this.showPackageWeight = false;
                }else if (this.battOrCell == "cell") {               
                    this.showFourBattAndButton = true;
                    this.showPackageWeight = false;
                }
            } else if (this.howPacked == "contained" && this.battOrCell == "battery" && this.wattHour <= 100 && this.isIon){
                //Check to see if this app;lies to everything
                this.showTwoBattOnly = true;
                this.showPackageWeight = false;
            } else if (this.howPacked == "contained" && this.battOrCell == "cell"){
                if (this.transport == "Ground") {
                    this.showFourBattOnly = true;
                    this.showPackageWeight = false;
                }
            } else if (this.howPacked == "loose" && this.transport == "Ground") {
                this.openReport = true;
            } else if (this.howPacked == "contained" && this.transport == "Air" && this.isMetal && ((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.packageWeight > 5) {
                this.gramsOption = this.battOrCell == "battery" ? this.gramsOption = "500 grams per battery" : this.gramsOption = "12 grams per cell";
                  this.showGramsQuestion = true;
                    this.showPackageWeight = false;     
            } else if (this.howPacked == "contained" && this.transport == "Air" && this.isMetal && ((this.battOrCell == "cell" && this.weightOfLi <= 1) || (this.battOrCell == "battery" && this.weightOfLi <= 2)) && this.packageWeight < 5) {
                if(this.battOrCell == "cell"){
                    this.showFourBattAndButton = true;
                    this.showPackageWeight = false;
                }else if(this.battOrCell == "battery"){
                    this.showTwoBattOnly = true;
                    this.showPackageWeight = false;
                }
            } else if (((this.wattHour > 20 && this.battOrCell == "cell") || (this.wattHour > 100 && this.battOrCell == "battery"))){
                this.showPackageWeight = false;
                this.showMoreThanNeeded = true;
            } else {
                this.openReport = true;
            }
        },
        handleTwoBattRailOnly() {
            if (this.twoBattAndButton == "more") {
                this.openReport = true;
            } else if (this.twoBattAndButton == "less") {
                this.showConsignment = true;
                this.showTwoBattAndButton = false;
            } else if (this.twoBattAndButton == "button") {
                this.openReport = true;
            }
        },
        handleASetQuestion() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            if (this.aSetAnswer == "less") {
                this.openReport = true;
            } else if (this.aSetAnswer == "more") {
                this.isOpen = true;
                this.aSetWarning = true;
            }
        },
        handleGramsQuestion() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            this.thirtyFiveKiloWarning = false;
            if (this.gramsAnswer == "true") {
                this.isOpen = true;
                this.gramsWarning = true;
            } else if (this.gramsAnswer == "false") {
                this.showMoreThanNeeded = true;
                this.showGramsQuestion = false;
            }
        },
        reportPicker() {
            this.reportLink = "/files/IonAir/IATA.US.PI965.IA.pdf";
        }
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

        maritimeLimiter() {
            if (this.battOrCell == "cell" && this.isIon && this.wattHour > 20) {
                return true;
            } else if (this.battOrCell == "battery" && this.isIon && this.wattHour > 100) {
                return true;
            } else if (this.battOrCell == "cell" && this.isMetal && this.weightOfLi > 1) {
                return true;
            } else if (this.battOrCell == "battery" && this.isMetal && this.weightOfLi > 2) {
                return true;
            }
            return false;
        },
        groundLimiter() {
            //Cells
            if (this.battOrCell == "cell" && this.isIon && this.wattHour > 20) {
                if (this.wattHour <= 60 && this.howPacked == "contained") {
                    //Need something here for report <=60 wh
                    return false;
                } else {
                    //Report >60 wh
                    return true;
                }
            } else if (this.battOrCell == "cell" && this.isMetal && this.weightOfLi > 1 && this.howPacked == "separate") {
                if (this.weightOfLi <= 5) {
                    //Less than 6g report
                    return true;
                } else {
                    //Greater than 6g report
                    return true;
                }
                //Batteries
            } else if (this.battOrCell == "battery" && this.isIon && this.wattHour > 100) {
                if (this.wattHour <= 300 && this.howPacked == "contained") {
                    //Report break
                    return false;
                } else {
                    //Report break > 300 grams
                    return true;
                }
            } else if (this.isMetal && ((this.battOrCell == "cell" && this.weightOfLi > 1) || (this.battOrCell == "battery" && this.weightOfLi > 2)) && this.howPacked == "separate") {
                if (this.weightOfLi <= 25) {
                    //Report break
                    return true;
                } else {
                    //Report break >25 grams of lithium content
                    return true;
                }
            } else if (this.isMetal && ((this.battOrCell == "cell" && this.weightOfLi > 5) || (this.battOrCell == "battery" && this.weightOfLi > 25)) && this.howPacked == "contained"){
                return true;
            }
            return false;
        },

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
    margin-top: 15px;

}

#show-report {
    margin: 10px;
}

.warning-text {
    color: red;
    font-weight: bold;
}

.bold-white {
    color: white;
    font-weight: bold;
}

.selection-block {
    display: block;
}

.button-wrapper {
    margin-top: 15px;
}

.nav-buttons {
    margin-left: 70px;
    margin-right: 70px;
}


.nav-buttons {
    background-image: linear-gradient(-180deg, #37AEE2 0%, #1E96C8 100%);
    border-radius: .5rem;
    box-sizing: border-box;
    color: #FFFFFF;
    font-size: 16px;
    font-weight: bold;
    padding: 0.5rem 0.75rem;
    text-decoration: none;
    width: auto;
    border: 0;
    cursor: pointer;
    user-select: none;
    -webkit-user-select: none;
    touch-action: manipulation;
}

.nav-buttons:hover {
    background-image: linear-gradient(-180deg, #1D95C9 0%, #17759C 100%);
}

#report-preview {
    width: auto;
    height: auto;
    border: black 2px solid;
}


.report-button {
    background: #FF4742;
    border: 1px solid #FF4742;
    border-radius: 6px;
    box-shadow: rgba(0, 0, 0, 0.1) 1px 2px 4px;
    box-sizing: border-box;
    color: #FFFFFF;
    cursor: pointer;
    display: inline-block;
    font-family: nunito, roboto, proxima-nova, "proxima nova", sans-serif;
    font-size: 16px;
    font-weight: 800;
    line-height: 16px;
    min-height: 40px;
    outline: 0;
    padding: 12px 14px;
    text-align: center;
    text-rendering: geometricprecision;
    text-transform: none;
    user-select: none;
    -webkit-user-select: none;
    touch-action: manipulation;
    vertical-align: middle;
}

.report-button:hover,
.report-button:active {
    background-color: initial;
    background-position: 0 0;
    color: #FF4742;
}

.report-button:active {
    opacity: .5;
}
</style>