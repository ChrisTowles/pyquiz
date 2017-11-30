<template>
    <div class="team-picker">
        <template v-if="selectedTeam === null">
            <h1>Team Picker</h1>

            <div class="player-choice" v-for="team in teamList" :key="team.id">
                <div class="player-select" @click="selectTeam(team.id)">
                    {{ team | teamName }}
                </div>
            </div>

            <div class="player-choice" v-if="showAdmin">
                <div class="player-select" @click="loginAdmin">
                    Admin
                </div>
            </div>

            <div class="loading" v-if="teamList == null">
                Loading...
            </div>
        </template>

        <template v-if="currentTeam != null && currentTeam.taken">
            <form @submit.prevent="rejoin">
                <label for="key">Rejoin Key:</label>
                <input type="password" v-model="key"><br /><br />
                <button type="submit" @click="rejoin">Rejoin</button>
            </form>
        </template>

        <template v-if="currentTeam != null && !currentTeam.taken">
            <form @submit.prevent="join">
                <label for="name">Team Name:</label>
                <input type="text" id="name" v-model="name" />
                <label for="key">Key:</label>
                <input type="text" id="key" v-model="key" />
                <button type="submit" @click="join">Join</button>
            </form>
        </template>

        <template v-if="adminLogin">
            <form @submit.prevent="adminJoin">
                <label for="password">Password:</label>
                <input type="password" id="password" v-model="key" />
                <button type="submit" @click="adminJoin">Login</button>
            </form>
        </template>
    </div>
</template>

<script>
    import {mapGetters} from 'vuex';

    export default {
        name: "team-picker",
        data() {
            return {
                selectedTeam: null,
                adminLogin: false,
                key: null,
                name: null,
            }
        },
        computed: {
            ...mapGetters(['teamList', 'getTeam']),
            currentTeam() {
                return this.getTeam(this.selectedTeam);
            },
            showAdmin() {
                return window.jeopardy.admin;
            },
        },
        methods: {
            selectTeam(id) {
                this.selectedTeam = id;
            },
            join() {
                this.$socket.emit('team.join', {
                    id: this.selectedTeam,
                    password: this.key,
                    name: this.name,
                });
            },
            rejoin() {
                this.$socket.emit('team.join', {
                    id: this.selectedTeam,
                    password: this.key,
                });
            },
            adminJoin() {
                this.$socket.emit('admin.login', {
                    password: this.key,
                });
            },
            loginAdmin() {
                this.adminLogin = true;
                this.selectedTeam = true;
            },
        }
    }
</script>

<style>
    .team-picker {
        text-align: center;
    }

    .player-select {
        color: white;
        text-decoration: underline;
        font-size: 14pt;
        margin: 6px;
    }
</style>