---
name: Manage mDash IoT devices
description: Register, update, remotely control (RPC), and OTA-update IoT devices on Cesanta's mDash cloud.
api: openapi/cesanta-mdash-openapi.yml
operations: [listDevices, registerDevice, updateDevice, callDeviceRpc, deviceOta, deleteDevice]
---

# Manage mDash IoT devices

Operating instructions for using Cesanta's mDash REST API to manage a fleet of
Mongoose OS devices.

## Authentication

Every request needs an `Authorization: Bearer KEY` header. The key is an mDash API
key created in the dashboard or via `createKey` (`POST /keys`). JSON bodies require
`Content-Type: application/json`. Base URL: `https://dash.mongoose-os.com/api/v2`.

## Steps

1. **List the fleet** — call `listDevices` (`GET /devices`) to see registered devices
   and their `id`, `name`, `shared_with`, and `shadow`.
2. **Register a device** — call `registerDevice` (`POST /devices`) to add a new device
   and obtain its credentials.
3. **Update properties** — call `updateDevice` (`POST /devices/{id}`) with any subset of
   `{name, shared_with, shadow}`. All keys are optional.
4. **Remote control** — call `callDeviceRpc` (`POST /devices/{id}/rpc/{func}`) with the
   JSON the target RPC function expects, e.g. `func=GPIO.Toggle` body `{"pin": 2}`.
5. **OTA update** — call `deviceOta` (`POST /devices/{id}/ota`) uploading a firmware
   `.zip` as `multipart/form-data` (`file=@fw.zip`).
6. **Retire a device** — call `deleteDevice` (`DELETE /devices/{id}`).

## Notes

- There is no idempotency-key mechanism; `registerDevice` and `createKey` are not
  idempotent, so avoid blind retries.
- The reference documents happy-path responses only; treat a `401` as a bad/missing
  bearer token (see errors/cesanta-problem-types.yml).
