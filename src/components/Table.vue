<template>
    <div class="container">
        <input class="search" type="text" v-model="searchVal">
        <div>
            <div class="table-containers">
                <table>
                    <thead>
                        <tr>
                            <th v-for="col in columns" @click="sortTable(col, true)">{{col}}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="row in displayData">
                        <td>{{row.id}}</td>
                        <td>{{row.name}}</td>
                        <td class="editable" @click="editField(row, true, 'side-effects')">{{row["side-effects"]}}</td>
                        <td class="editable" @click="editField(row, true, 'effectiveness')">{{row.effectiveness}}</td>
                        <td class="editable note" @click="editField(row, false, 'note')">{{row.note}}</td>
                        <td>
                            <button v-if="row.saved" @click="clickedAddRemove(row, false)">Remove</button>
                            <button v-else @click="clickedAddRemove(row, true)">Add</button>
                        </td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="table-containers">
                <table>
                    <thead>
                        <tr>
                            <th v-for="col in columns" v-on:click="sortTable(col, false)">{{col}}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="row in savedData">
                            <td>{{row.id}}</td>
                            <td>{{row.name}}</td>
                            <td class="editable" @click="editField(row, true, 'side-effects')">{{row["side-effects"]}}</td>
                            <td class="editable" @click="editField(row, true, 'effectiveness')">{{row.effectiveness}}</td>
                            <td class="editable note" @click="editField(row, false, 'note')">{{row.note}}</td>
                                <button v-if="row.saved" @click="clickedAddRemove(row, false)">Remove</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="edit" v-if="editing">
                <p v-if="editingNumerical">View or edit number on a scale of 1-5</p>
                <p v-else>View/edit note</p>
                <input v-if="editingNumerical" type='number' v-model="editVal"></input>
                <input v-else type='text' v-model="editVal"></input>
                <button @click="submit()">Submit</button>
                <p class="error" v-if="submitError">Please enter a number between 1-5</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        rows: Array,
    },
    data() {
        return {
            savedData: [],
            displayData: this.rows,
            searchVal: '',
            editing: false,
            editingNumerical: false,
            editingDrug: null,
            field: '',
            editVal: '',
            submitError: false,
            columnToSort: '',
            ascending: false
        }
    },
    computed: {
        "columns": function columns() {
        if (this.rows.length == 0) {
        return [];
        }
        return Object.keys(this.rows[0]);
        }
    },
    watch: {
        // Search function
        searchVal: function (newSearchVal, oldSearchVal) {
            let dataToFilter = this.rows;
            newSearchVal = newSearchVal.trim();
            this.displayData = dataToFilter.filter(row => row.name.toLowerCase().substring(0, newSearchVal.length) == newSearchVal);
        }
    },
    methods: {
        // All in one add/remove to user table
        clickedAddRemove: function(row, add) {
            if (add) {
                if (!this.savedData.some(curr => curr.id == row.id)) {
                    // Update the mock data(base)
                    row.saved = true;
                    let foundIndex = this.rows.findIndex(curr => curr.id == row.id);
                    this.rows[foundIndex] = row;
                    this.savedData.push(row);
                }
            } else {
                // Update the mock data(base) that the user no longer has added the drug
                row.saved = false;
                let foundIndex = this.rows.findIndex(curr => curr.id == row.id);
                this.rows[foundIndex] = row;

                foundIndex = this.savedData.findIndex(curr => curr.id == row.id);
                this.savedData.splice(foundIndex, 1);
            }
        },

        editField: function(row, numericalEdit, field) {
            this.editing = true;
            this.editingNumerical = numericalEdit;
            this.editingDrug = row;
            this.field = field;
            this.editVal = row[field];
        },

        submit: function() {
            let field = this.field;
            this.editingDrug[field] = this.editVal;
            if (this.editingNumerical) {
                if (parseInt(this.editVal) < 1 || parseInt(this.editVal) > 5) {
                    this.submitError = true;
                    return;
                }
                this.editingDrug[field] = parseInt(this.editingDrug[field]);
            }

            let foundIndex = this.rows.findIndex(curr => curr.id == this.editingDrug.id);
            this.rows[foundIndex] = this.editingDrug;

            // Clear the editing data
            this.submitError = false;
            this.editing = false;
            this.editingDrug = null;
            this.field = '';
            this.editVal = '';
        },

        sortTable: function(column, isMainTable) {
            if (column == this.columnToSort) {
                this.ascending = !this.ascending;
            }
            else {
                this.columnToSort = column;
                this.ascending = true;
            }
                
            let arrayName = isMainTable ? "displayData" : "savedData";
            this[arrayName].sort(function(a, b) {
                if (a[column] > b[column]) {
                    return 1;
                } else if (a[column] < b[column]) {
                    return -1;
                } else {
                    if(a.id > b.id)
                        return -1;
                    else
                        return 1;
                }
            });

            if (!this.ascending)
                isMainTable ? this.displayData.reverse() : this.savedData.reverse();
        }
    }
}
</script>

<style scoped>
@import '../assets/base.css';
.container {
    min-width: 1000px;
    height: 100%
}
table {
    width: 50%;
    display: block;
    float: left;
    border: 1px solid white;
    overflow-y: scroll;
    height: 500px;

}
table th {
    position: sticky;
    top: 0;
    text-transform: uppercase;
    text-align: left;
    min-width: 80px;
    border: 1px solid white;
    padding: 0px 10px;
    box-shadow: 0 2px 2px -1px rgba(0, 0, 0, 0.4);
    background-color: #181818;
    z-index: 2;
}

table td {
    text-align: left;
    border: 1px solid white;
    padding: 0px 10px;
}

.note {
    word-wrap:break-word;
}
.edit {
    width: 250px;
    height: 30px;
}

.editable {
    cursor: pointer;
}

.error {
    color: #D62D20;
}


</style>