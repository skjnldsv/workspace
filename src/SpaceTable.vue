<!--
  @copyright Copyright (c) 2017 Arawa

  @author 2021 Baptiste Fotia <baptiste.fotia@arawa.fr>
  @author 2021 Cyrille Bollu <cyrille@bollu.be>

  @license GNU AGPL version 3 or any later version

  This program is free software: you can redistribute it and/or modify
  it under the terms of the GNU Affero General Public License as
  published by the Free Software Foundation, either version 3 of the
  License, or (at your option) any later version.

  This program is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  GNU Affero General Public License for more details.

  You should have received a copy of the GNU Affero General Public License
  along with this program.  If not, see <http://www.gnu.org/licenses/>.
-->

<template>
	<div class="main-div">
		<div class="header" />
		<table v-if="Object.keys($store.state.spaces).length" class="table-spaces">
			<thead>
				<tr>
					<th />
					<th>{{ t('workspace', 'Workspace name') }}</th>
					<th>{{ t('workspace', 'Quota') }}</th>
					<th>{{ t('workspace', 'Space administrators') }}</th>
				</tr>
			</thead>
			<tr
				v-for="(space,name) in $store.state.spaces"
				:key="name"
				@click="openSpace(name)">
				<td style="width: 50px;">
					<span class="color-dot-home" :style="{background: space.color}" />
				</td>
				<td> {{ name }} </td>
				<td> {{ space.quota }} </td>
				<td>
					<div class="admin-avatars">
						<Avatar
							v-for="user in workspaceManagers(space)"
							:key="user.uid"
							:style="{ marginRight: 2 + 'px' }"
							:display-name="user.name"
							:user="user.uid" />
					</div>
				</td>
			</tr>
		</table>
		<EmptyContent v-else>
			<p>{{ t('workspace', 'No spaces') }}</p>
			<template #desc>
				{{ t('workspace', 'You have not yet created any workspace') }}
			</template>
		</EmptyContent>
	</div>
</template>

<script>
import Avatar from '@nextcloud/vue/dist/Components/Avatar'
import EmptyContent from '@nextcloud/vue/dist/Components/EmptyContent'

export default {
	name: 'SpaceTable',
	components: {
		Avatar,
		EmptyContent,
	},
	methods: {
		convertQuotaForFrontend(quota) {
			if (quota === '-3') {
				return t('workspace', 'unlimited')
			} else {
				const units = ['', 'KB', 'MB', 'GB', 'TB']
				let i = 0
				while (quota >= 1024) {
					quota = quota / 1024
					i++
				}
				return quota + units[i]
			}
		},
		// Returns all workspace's managers
		workspaceManagers(space) {
			return Object.values(space.users).filter((u) => this.$store.getters.isSpaceAdmin(u, space.name))
		},
		openSpace(name) {
			this.$store.state.spaces[name].isOpen = true
			this.$router.push({
				path: `/workspace/${name}`,
			})
		},
	},
}
</script>

<style>
.admin-avatars {
	display: flex;
	flex-flow: row-reverse;
}

.color-dot-home {
	height: 35px;
	width: 35px;
	border-radius: 50%;
	display: block;
}

.table-spaces {
	width: 100%;
	margin-top: -81px;
}

.main-div {
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: space-between;
}

</style>
