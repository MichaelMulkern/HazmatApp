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
            <select class="dropdown" v-model="cellsInPkg">
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

        <div id="two-batt-rail" class="selection-block" v-if="showTwoBattRailOnly">
            <h3>Does the package contain more than 2 batteries installed in the equipment?</h3>
            <select class="dropdown" v-model="cellsInPkgRailOnly">
                <option class="drop-option" value="more">Contains more than 4 cells</option>
                <option class="drop-option" value="less">Contains 4 cells or less</option>
                <option class="drop-option" value="button">Contains button cells (includes circuit boards)</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleTwoBattRailOnly()">NEXT</button>
            </div>
        </div>

        <div id="two-batt" class="selection-block" v-if="showTwoBatt">
            <h3>Does the package contain more than 2 batteries installed in the equipment?</h3>
            <select class="dropdown" v-model="twoBattAnswer">
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
                <option class="drop-option" value="true">Consignment contains no more than two such Section II packages
                </option>
                <option class="drop-option" value="false">Consignment contains more than two such Section II packages
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


        <div id="four-or-more" class="selection-block" v-if="showMoreThanFour">
            <h3 class="question-header">Does the package contain more than 4 cells or button cells installed in the
                equipment?</h3>
            <select class="dropdown" v-model="cellsInPkg">
                <option class="drop-option" value="more">Contains more than 4 cells</option>
                <option class="drop-option" value="less">Contains 4 cells or less</option>
                <option class="drop-option" value="button">Contains button cells (includes circuit boards)</option>
            </select>
            <div class="button-wrapper">
                <button class="nav-buttons" v-on:click="handleBack(6)">BACK</button><button class="nav-buttons"
                    v-on:click="handleMoreThanFourCells()">NEXT</button>
            </div>
        </div>

        <div id="four-or-more-rail-only" class="selection-block" v-if="showFourBattRailOnly">
            <h3 class="question-header">Does the package contain more than 4 cells installed in the equipment?</h3>
            <select class="dropdown" v-model="fourBattRailOnly">
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
            </warningModal>
        </div>
        <!--REPORT BELOW THIS LINE-->
        <button id="show-report" v-if="showReportButton" v-on:click="handleShowReport()">{{ reportButton }}</button>

        <div id="report-preview" v-if="showReport">
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
import warningModal from "@/components/WarningModal.vue";
import { ref } from "vue";
export default {
    name: "BatteryTree",
    components: { warningModal },
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
            showTwoBatt: false,
            twoBattAnswer: "",
            wattHour: 0,
            showWh: false,
            showReport: false,
            showPkgWeight: false,
            reportButton: "Show Report Preview",
            battsInPkg: "",
            showBattsInPkg: false,
            showMoreThanFour: false,
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
            showReportButton: false,
            previousMenu: "",
            showTransport: true,
            socWarning: false,
            moreThanTwoWarning: false,
            cellsInPkg: "",
            moreThanNeededWarning: false,
            showTwoBattRailOnly: false,
            cellsInPkgRailOnly: "",
            showASetQuestion: false,
            aSetAnswer: "",
            aSetWarning: false,
            gramsOption: "",
            showGramsQuestion: false,
            gramsAnswer: "",
            gramsWarning: false,
            fourBattRailOnly: "",
            showFourBattRailOnly: false,

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
                    this.showUsa = true
                    this.showBattOrCell = false
                    break;
                case 4:
                    this.showWh = false
                    this.showWeight = false
                    this.showReportButton = false
                    this.showBattOrCell = true
                    break;
                case 5:
                    if (this.isIon) {
                        this.showWh = true;
                        this.showPackageWeight = false;
                        this.showReportButton = false;
                    } else if (this.isMetal) {
                        this.showWeight = true;
                        this.showPackageWeight = false;
                        this.showReportButton = false;
                        this.showFourBattRailOnly = false;
                    }
                    break;
                case 6:
                    if (this.transport == "Ground" && this.howPacked == "contained") {
                        this.showWeight = true;
                        this.showMoreThanFour = false;
                        this.showReportButton = false;
                        this.showBattsInPkg = false;
                        this.showStateOfCharge = false;
                        this.showTwoBatt = false;
                        this.showTwoBattRailOnly = false;
                        this.showASetQuestion = false;
                        this.showGramsQuestion = false;
                    } else {
                        this.showMoreThanFour = false;
                        this.showReportButton = false;
                        this.showBattsInPkg = false;
                        this.showStateOfCharge = false;
                        this.showTwoBatt = false;
                        this.showTwoBattRailOnly = false;
                        this.showASetQuestion = false;
                        this.showGramsQuestion = false;
                        this.showPackageWeight = true;
                    }
                    break;
                case 7:
                    this.showReportButton = false;
                    this.showMoreThanNeeded = false;
                    this.showGramsQuestion = false;
                    if (this.battOrCell == "battery" && this.isIon) {
                        this.showTwoBatt = true;
                    } else if (this.battOrCell == "cell" && this.isIon) {
                        this.showMoreThanFour = true;
                    } else if (this.transport == "Air" && this.isMetal) {
                        this.showGramsQuestion = true;
                    }
                    break;
                case 8:
                    this.showReportButton = false;
                    this.showSectionTwoConsignment = false;
                    this.showMoreThanNeeded = true;
                    break;
                case 9:
                    this.showReportButton = false;
                    if (this.isIon) {
                        if (this.battOrCell == "battery") {
                            this.showTwoBatt = true;
                            this.showConsignment = false;
                        } else if (this.battOrCell == "cell") {
                            this.showMoreThanFour = true;
                            this.showConsignment = false;
                        }
                    } else if (this.isMetal) {
                        if (this.battOrCell == "cell") {
                            this.showFourBattRailOnly = true;
                            this.showConsignment = false;
                        } else if (this.battOrCell == "battery") {
                            this.showTwoBatt = true;
                            this.showConsignment = false;
                        }
                    }
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
                this.showReportButton = true;
            } else if (this.transport == "Ground" && this.isMetal && this.howPacked == "separate") {
                this.showReportButton = true;
            } else if (this.transport == "Ground" && this.isMetal && this.howPacked == "contained" && this.battOrCell == "cell") {
                this.showFourBattRailOnly = true;
                this.showWeight = false;
            } else if (this.transport == "Ground" && this.isMetal && this.howPacked == "contained" && this.battOrCell == "battery") {
                this.showTwoBatt = true;
                this.showWeight = false;
            }
            else {
                this.showPackageWeight = true;
                this.showWeight = false;
                this.showWh = false;
            }
        },
        handleShowReport() {
            if (this.showReport) {
                this.reportButton = "Show Report Preview"
                this.showReport = false;
            } else {
                this.reportButton = "Hide Report Preview"
                this.showReport = true;
            }
        },
        handleMoreThanFourCells() {
            if (this.cellsInPkg == "more") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showReportButton = true;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showMoreThanFour = false;
                }
            } else if (this.cellsInPkg == "less") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showConsignment = true;
                    this.showMoreThanFour = false;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showMoreThanFour = false;
                }
            } else if (this.cellsInPkg == "button") {
                this.showReportButton = true;
            }
        },
        handleMoreThanFourRailOnly() {
            if (this.fourBattRailOnly == "more") {
                this.showReportButton = true;
            } else if (this.fourBattRailOnly == "less") {
                this.showConsignment = true;
                this.showFourBattRailOnly = false;
                this.showReportButton = false;
            }
        },
        handleMoreThanNeededToPower() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            if (this.moreThanNeeded == "true") {
                this.moreThanNeededWarning = true;
                this.isOpen = true;
            } else if (this.moreThanNeeded == "false") {
                if (this.isIon && this.battOrCell == "cell" && this.transport == "Air") {
                    if (this.cellsInPkg == "more") {
                        this.showReportButton = true;
                    } else if (this.cellsInPkg == "less") {
                        this.showSectionTwoConsignment = true;
                        this.showMoreThanNeeded = false;
                    }
                } else if (this.isMetal && this.transport == "Air") {
                    this.showReportButton = true;
                } else if (this.isIon && this.twoBattAnswer == "true" && this.transport == "Air") {
                    this.showReportButton = true;
                } else if (this.isIon && this.twoBattAnswer == "false" && this.transport == "Air") {
                    this.showSectionTwoConsignment = true;
                    this.showMoreThanNeeded = false;
                }
            }
        },
        handleNumberContainedInConsignment() {
            if (this.amountInConsignment == "true" || this.amountInConsignment == "false") {
                this.showReportButton = true;
            }
        },
        handleTwoBatt() {
            if (this.twoBattAnswer == "true") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showReportButton = true;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showTwoBatt = false;
                }
            } else if (this.twoBattAnswer == "false") {
                if (this.transport == "Ocean" || this.transport == "Ground") {
                    this.showConsignment = true;
                    this.showTwoBatt = false;
                } else if (this.transport == "Air") {
                    this.showMoreThanNeeded = true;
                    this.showTwoBatt = false;
                }
            }
        },
        handleNumberOfBattsInPackage() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            if (this.battsInPkg == "false") {
                this.moreThanTwoWarning = true;
                this.isOpen = true;
            } else {
                this.showReportButton = true;
            }
        },
        handleStateOfCharge() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            //Air only max 30% charge 
            if (this.stateOfCharge > 30) {
                this.isOpen = true;
                this.socWarning = true;
            } else {
                this.showReportButton = true;
            }
        },
        handleSectionTwoPackages() {
            if (this.sectionTwoConsignment == "true" || this.sectionTwoConsignment == "false") {
                this.showReportButton = true;
            }
        },
        handlePackageJunction() {
            if (this.transport == "Air" && this.isIon && this.howPacked == "loose") {
                this.showStateOfCharge = true;
                this.showPackageWeight = false;
            } else if (this.transport == "Air" && this.howPacked == "separate") {
                this.showBattsInPkg = true;
                this.showPackageWeight = false;
            } else if (this.howPacked == "contained" && this.battOrCell == "cell" && this.isIon) {
                //Transport doesn't matter
                this.showMoreThanFour = true;
                this.showPackageWeight = false;
            } else if (this.howPacked == "contained" && this.battOrCell == "battery" && this.isIon) {
                if (this.transport == "Ground") {
                    this.showTwoBattRailOnly = true;
                    this.showPackageWeight = false;
                } else {
                    this.showTwoBatt = true;
                    this.showPackageWeight = false;
                }
            } else if (this.howPacked == "contained" && this.transport == "Air" && this.isMetal) {
                if (this.battOrCell == "battery") {
                    this.gramsOption = "500 grams per battery";
                } else if (this.battOrCell == "cell") {
                    this.gramsOption = "12 grams per cell";
                }
                this.showGramsQuestion = true;
                this.showPackageWeight = false;
            } else {
                this.showReportButton = true;
            }
        },
        handleTwoBattRailOnly() {
            if (this.cellsInPkgRailOnly == "more") {
                this.showReportButton = true;
            } else if (this.cellsInPkgRailOnly == "less") {
                this.showConsignment = true;
                this.showTwoBattRailOnly = false;
            } else if (this.cellsInPkgRailOnly == "button") {
                this.showReportButton = true;
            }
        },
        handleASetQuestion() {
            this.socWarning = false;
            this.moreThanTwoWarning = false;
            this.moreThanNeededWarning = false;
            this.aSetWarning = false;
            this.gramsWarning = false;
            if (this.aSetAnswer == "less") {
                this.showReportButton = true;
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
            if (this.gramsAnswer == "true") {
                this.isOpen = true;
                this.gramsWarning = true;
            } else if (this.gramsAnswer == "false") {
                this.showMoreThanNeeded = true;
                this.showGramsQuestion = false;
            }
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
            if(this.battOrCell == "cell" && this.isIon && this.wattHour > 20){
                return true;
            }else if(this.battOrCell == "battery" && this.isIon && this.wattHour > 100){
                return true;
            }else if(this.battOrCell == "cell" && this.isMetal && this.weightOfLi > 1){
                return true;
            }else if(this.battOrCell == "battery" && this.isMetal && this.weightOfLi > 2){
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
</style>