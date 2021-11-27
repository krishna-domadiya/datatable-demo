<template>
    <div class="container">
        <div class="row mt-3">
            <div class="col-sm">
                <span> Show </span>
                <select v-model="selectedRecords" @change="changeEntries">
                    <option v-for="item in showRecords" :key="item">{{item}}</option>
                </select>
            </div>
            <div class="col-sm text-right">
                <input type="text" v-model="search" placeholder="Search"/>
                <input class="btn btn-info" type="button" name="search" value="Search" @click="searchIntoDatatable"/>
            </div>
        </div>
        <div class="mt-3">
            <table class="table">
                <tr>
                    <th v-for="item in headers" :key="item.name" @click="sortDatatable(item.name)" style="cursor: pointer;">{{item.text}}</th>
                </tr>
                <tr v-for="item in filteredRecordsData" :key="item.id">
                    <td>{{item.id}}</td>
                    <td>{{item.username}}</td>
                    <td>{{item.first_name}}</td>
                    <td>{{item.last_name}}</td>
                    <td>{{item.email}}</td>
                    <td>{{item.gender}}</td>
                    <td>{{item.dob}}</td>
                    <td>{{item.country}}</td>
                </tr>
            </table>
        </div>
        <div class="row mb-3">
            <div class="col-sm">
                <span> Show {{entriesInfo.from}} to {{entriesInfo.to}} of {{entriesInfo.total}} entries </span>
            </div>
            <div class="col-sm text-right">
                <span> 
                    <input class="btn btn-secondary" type="button" name="prev" value="prev" :disabled="currentPage == 0" @click="paginateToPrev"/>
                    <input class="btn btn-secondary" type="button" name="current" :value="Math.floor(currentPage/selectedRecords)+1" disabled/>
                    <input class="btn btn-secondary" type="button" name="next" value="next" @click="paginateToNext"/>
                </span>
            </div>
        </div>
    </div>
</template>
<script>
import json from '/static/users.json'
export default {
    data() {
        return {
            headers: [
                {name:"id", text: "ID"},
                {name:"username", text: "Username"},
                {name:"first_name", text: "First Name"},
                {name:"last_name", text: "Last Name"},
                {name:"email", text: "Email"},
                {name:"gender", text: "Gender"},
                {name:"dob", text: "DOB"},
                {name:"country", text: "Country"},
            ],
            records: json,
            recordsBackup: {},
            showRecords: [10, 50, 100, 500, 1000],
            selectedRecords: 10,
            currentPage: 0,
            search: "",
            filteredRecordsData: {},
            blnChangeEntries: false,
        }
    },
    methods: {
        changeEntries() {
            this.filteredRecordsData    =   this.records.slice(this.currentPage, this.selectedRecords);
        },
        paginateToNext() {
            this.currentPage += parseInt(this.selectedRecords);
            console.log(this.currentPage, parseInt(this.selectedRecords)+parseInt(this.currentPage));
            this.filteredRecordsData    =   this.records.slice(this.currentPage, parseInt(this.selectedRecords)+parseInt(this.currentPage));
        },
        paginateToPrev() {
            console.log(parseInt(this.currentPage)-parseInt(this.selectedRecords), this.currentPage);
            this.filteredRecordsData    =   this.records.slice(parseInt(this.currentPage)-parseInt(this.selectedRecords), parseInt(this.currentPage));
            this.currentPage -= parseInt(this.selectedRecords);
        },
        searchIntoDatatable() {
            this.blnChangeEntries  = true;
            this.records    =   this.recordsBackup;
            if(this.search)
            {
                this.records    =   this.records.filter(value => { 
                        return (value.first_name && value.first_name.toLowerCase().indexOf(this.search.toLowerCase()) > -1) 
                            || (value.last_name && value.last_name.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
                            || (value.username && value.username.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
                            || (value.email && value.email.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
                            || (value.country && value.country.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
                            || (value.gender && value.gender.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
                    });
            }
            this.changeEntries();
        },
        sortDatatable(columnName) {
            this.records = this.records.sort((a,b) => (a.columnName > b.columnName) ? 1 : ((b.columnName > a.columnName) ? -1 : 0))
        }
    },
    computed: {
        entriesInfo() {
            console.log(this.records);
            return (!this.blnChangeEntries ? {
                from: this.filteredRecordsData[0].id,
                to: this.filteredRecordsData[this.filteredRecordsData.length - 1].id,
                total: this.records.length
            } : {
                from: 1,
                to: this.records.length,
                total: this.records.length
            })
        },
    },
    created() {
        this.recordsBackup  =   this.records;
        this.changeEntries();
    }
}

</script>

<style>

</style>
