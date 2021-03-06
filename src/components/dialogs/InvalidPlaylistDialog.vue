<template>
  <v-dialog v-model="open" max-width="80%" @click:outside="closeDialog">
    <v-card>
      <v-card-title>
        Invalid Playlists
      </v-card-title>
      <v-card-text>
        <v-simple-table dense fixed-header>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">
                  Folder
                </th>
                <th class="text-left">
                  Reason
                </th>
                <th class="text-left">
                  More info
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in invalidPlaylist" :key="item.path">
                <td>{{ item.path }}</td>
                <td class="error--text">
                  {{ item.loadState.errorType | errorTranslated }}
                </td>
                <td>
                  {{ item.loadState.errorMessage }}
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn text @click="closeDialog">
          Ok
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import Vue from "vue";
import PlaylistLibrary from "@/libraries/playlist/PlaylistLibrary";
import PlaylistLoadStateError from "@/libraries/playlist/loader/PlaylistLoadStateError";

export default Vue.extend({
  name: "InvalidBeatmapDialog",
  filters: {
    errorTranslated: (error: PlaylistLoadStateError): string => {
      switch (error) {
        case PlaylistLoadStateError.PathDoesntExist:
          return "The playlist doesn't even exist";
        case PlaylistLoadStateError.FormatDoesntExist:
          return "This playlist format doesn't exist (judging from the file extension name).";
        case PlaylistLoadStateError.FailedToParse:
          return "Failed to parse the playlist.";
        case PlaylistLoadStateError.Unknown:
        default:
          return "Unknown error";
      }
    },
  },
  props: {
    open: { type: Boolean, required: true },
  },
  computed: {
    invalidPlaylist: () => PlaylistLibrary.GetAllInvalidPlaylists(),
  },
  methods: {
    closeDialog() {
      this.$emit("update:open", false);
    },
  },
});
</script>
