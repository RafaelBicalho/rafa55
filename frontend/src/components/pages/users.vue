<template>
	<div>
		<h1>Users</h1>
		<div>
			<template v-for="(user, index) in users" >
				<div v-if="user._id != loggedUser._id" class="card" style="margin-top: 15px;">
				  <div class="card-header"><h3 style="display: inline;">Email: {{user.email}}</h3></div>
				  <div class="card-body">
				  	<h5>
				  		Name: {{user.username}}
				  	</h5>
				  </div>
				  <div class="card-footer">
				  	<button v-if="!isFollowed(user)" class="btn btn-primary" @click="follow(user)">Follow</button>
				  	<button v-else class="btn btn-primary" @click="unfollow(user)">Unfollow</button>
				  </div>
				</div>
			</template>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				showLoading: false,
				users: []
			}
		},

		methods: {
			getUsers() {
				this.showLoading = true;
				this.axios.get('http://localhost:3001/api/users', {
				    headers: { authorization: localStorage.getItem('jwt') }
				}).then(response => {
					console.log(response.data);
					this.users = response.data;
				}).catch(error => {
					console.log(error);
				});

			},
			isFollowed(user) {
				var returnValue = false;
				user.followers.forEach(follower => {
					if(follower._id == this.loggedUser._id) {
						returnValue = true;
					}
				});
				return returnValue;
			},
			follow(user) {
				this.axios.post('http://localhost:3001/api/user/follow', {
					user: user._id
				},  {
				    headers: { authorization: localStorage.getItem('jwt') }
				}).then(response => {
					console.log(response.data);
					user.followers.push(this.loggedUser);
				}).catch(error => {
					console.log(error);
				});
			},

			unfollow(user) {
				this.axios.post('http://localhost:3001/api/user/unfollow', {
					user: user._id
				},  {
				    headers: { authorization: localStorage.getItem('jwt') }
				}).then(response => {
					console.log(response.data);
					for (var i = 0; i < user.followers.length; i++) {
						if(this.loggedUser._id == user.followers[i]._id) {
							user.followers.splice(i, 1);
							break;
						}
					}
				}).catch(error => {
					console.log(error);
				});
			}
		},

		created() {
			this.getUsers();
		},

		computed: {
			loggedUser() {
				return JSON.parse(localStorage.getItem('authUser'));
			}
		}
	}
</script>