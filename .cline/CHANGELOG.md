# Cline Changelog

---
#### [2026-09-13 16:51:39] Task: Playlist sync via Google OAuth + default playlist menu
- **User Prompt**: "Change links and playlists handling logic (main goal - fetch user's lists automatically): 1) maintain same visual style; 2) set/keep default playlist in localStorage, set/delete from a menu; 3) load playlists from user's YouTube account, only playlists with name starting with 'Radio)'"
- **Files Modified**:
  - `index.html`: 
    - New constants: `GOOGLE_CLIENT_ID` (user's OAuth client), `GOOGLE_API_SCOPE` (youtube.readonly), `RADIO_PREFIX = 'Radio)'`, `DEFAULT_PLAYLIST_ID = 'PL01N4uJROxb-ltHoQAYr337d6ssDPxpNe'` (replaced old default), `DEFAULT_PLAYLIST_NAME`
    - New localStorage keys: `radio_default_playlist` ({id,name}, seeded on first run), `radio_sync_hidden` (deleted auto playlists), `radio_sync_time` (last sync), `radio_session_playlist` (one-time switch override)
    - New startup resolution `resolveStartupPlaylistId()`: session → default → last played → fallback; `radio_last_pos` now stores `pid` and resume only applies if pid matches current playlist
    - Google Identity Services script loaded at startup; `syncFromYouTube()` → token client → `fetchRadioPlaylists()` (YouTube Data API v3 `playlists?mine=true`, paginated, Bearer token) → `mergeRadioPlaylists()` (filter `name.trim().startsWith('Radio)')`, auto:true flag, keeps manual names, respects hidden list)
    - Playlist Manager modal (same glass style): "Sync from YouTube ('Radio)')" button with Google logo, `default-indicator` line, `sync-status` line, "Clear Default" button; playlist rows now show `auto` badge, star button (set default; cyan when default), delete button
    - `deletePlaylistFromList()`: hides auto playlists from future syncs, clears default if it was deleted
    - `renderSavedPlaylists()`: reworked with escapeHtml for names, uses PLAYLIST_ID for current highlight
    - `switchToPlaylist()`: sets `radio_session_playlist` so switching works while default is kept for next startup
    - Version bumped to `2026-09-13-`
- **Commands Executed**: `git init` + checkpoint commit `898b3fb`; backup `.backups/backup_20260913_165139/`; headless Chrome smoke test (no JS errors); functional self-test via /tmp copy (seeding, default set/clear, prefix filter, merge, delete-hidden all passed)
- **Rollback Instructions**: `git checkout <this-commit>^ -- index.html` or restore `.backups/backup_20260913_165139/index.html`. New localStorage keys can be removed via `localStorage.removeItem('radio_default_playlist'/'radio_sync_hidden'/'radio_sync_time'/'radio_session_playlist')` or the in-app Reset All button.
---
