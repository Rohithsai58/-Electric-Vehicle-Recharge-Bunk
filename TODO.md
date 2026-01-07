# TODO: Phase 1 & 2 Fixes

## Phase 1: Backend Core Updates
- [x] Update Station Model (server/models/stationsModel.js): Add status, isVisible, latitude, longitude fields
- [x] Create Nearest Stations Endpoint (server/data/stations.js): Add getNearestStations function
- [x] Create Admin Dashboard Summary Endpoint (server/data/admin.js): Add getDashboardSummary function
- [x] Add Routes (server/routes/stations.js): Add GET /nearest
- [x] Add Routes (server/routes/admin.js): Add GET /dashboard

## Phase 2: Frontend Updates
- [x] Update nearest stations fetch (client/src/data/booking.js): Modify getNearestStations to use new endpoint
- [x] Show stations after Add Station (client/src/components/forms/AddStationForm.jsx): Ensure create function sends status and isVisible
