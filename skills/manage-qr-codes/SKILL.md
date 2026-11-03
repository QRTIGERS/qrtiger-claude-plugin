---
name: manage-qr-codes
description: List, create, design, organize, track, archive, and delete QR Tiger QR codes for the signed-in account. Use when the user wants to manage QR campaigns, folders, designs, or scan analytics.
---

# Manage QR codes

Work only on the signed-in QR Tiger account. Use `get_dashboard` or `verify_connection` for the account-wide QR total (`totalQrs`). `list_campaigns` `query.total` is a filtered count and excludes bulk.

Before any edit, design change, archive, restore, folder change, member change, ownership change, scan reset, or delete, ask the user to confirm, then call the tool with `confirmed=true`.

Pass a small `limit` on list calls, including `list_campaigns` and `list_folders`, so the result stays under Claude's size limit. If a result is still too large, narrow it with `page`, `limit`, or a single `qrId`.

Do not call `list_campaigns_api`, `edit_campaign_api`, or `delete_campaign_api`. Use the tools below.

## Find and inspect

- `list_campaigns` lists campaigns. Pass a small `limit`. Other filters: `page`, `search`, `category`, `sort` (1 created, 2 name, 3 scans, 4 updated), `subUserId`. Set `archive=true` to list archived QR codes.
- `get_qr_image` returns the image and details for a `qrId`.
- `download_qr` downloads one QR. Optional `format` is png, svg, pdf, or eps. Optional `resolution` defaults to 256. Pass `type=gs1` for a GS1 image.
- `download_qr_list_csv` exports the campaign list. Optional `archive` and `category`.
- `get_qr_action_history` returns edit history for a `qrId`.
- `get_watchlist` and `get_top_qr_codes` show watched and top-performing QR codes. `update_watchlist` adds or removes one QR after confirmation.

## Create

Pass the API body in `payload` for dynamic creates. Static creates need `text`.

Dynamic categories, one tool each:

- `create_dynamic_qr` for a URL
- `create_vcard_qr`, `create_file_qr`, `create_social_media_qr`, `create_menu_qr`, `create_html_qr`
- `create_google_form_qr`, `create_app_stores_qr`, `create_mp3_qr`
- `create_facebook_qr`, `create_youtube_qr`, `create_instagram_qr`, `create_pinterest_qr`
- `create_campaign` for any other dynamic campaign, including multi-URL location (`qrType=murl`, `qrCategory=location`)

Static-only types. Do not create these with `create_campaign`:

- `create_event_qr` with iCalendar or VEVENT text
- `create_sms_qr` with `smsto:` text
- `create_location_qr` with `geo:` text
- `create_wifi_qr` with `WIFI:S:...` text
- `create_email_qr` with a `mailto:` link
- `create_text_qr` with plain text
- `create_static_qr` when the category is not one of the tools above. Optional `qrFormat` is png, svg, or pdf.

`clone_campaign` copies an existing campaign by `qrId`.

## Edit and design

- `edit_campaign` updates a campaign. Requires `qrId` and `payload`.
- `design_qr` previews a design and can save `qrConfig`. Pass the design body in `payload`.
- `update_qr_design` changes the saved design of an existing `qrId`.
- `update_campaign_members` changes who is assigned to a `qrId`.
- vCard slugs are Enterprise only. `check_vcard_slug` checks availability. `set_vcard_custom_slug` sets one. `delete_vcard_custom_slug` removes one from a `qrId`.

## Folders

- `list_folders` and `get_folder` (by `folderId`) show folders and the QR codes inside one folder. Pass a small `limit` on `list_folders`.
- `create_folder` takes the folder body in `payload` (name, members).
- `update_folder` changes name or members.
- `delete_folder` removes the folder and leaves the QR codes in the account.
- `move_qr_to_folder` moves QR codes into a `folderId`.
- `remove_qr_from_folder` takes `qrId` and `folderId`.

## Archive, restore, and delete

An active QR cannot be permanently deleted.

1. Ask the user, then call `archive_campaign` with `confirmed=true`. For several QR codes, use `archive_campaigns_multi` with `qrIds`.
2. Ask again if they want a permanent delete.
3. Only after that confirmation, call `delete_campaign` or `delete_archived_campaigns_multi` with `confirmed=true`. The QR must already be archived. Permanent delete cannot be undone.

`restore_campaign` and `restore_campaigns_multi` put archived QR codes back. Use `list_campaigns` with `archive=true` and a small `limit` to see archived QR codes first.

## Scan analytics

Pass `qrId` unless noted.

- `get_qr_stats` overview, `get_unique_scans` unique scans
- `get_device_stats`, `get_location_stats`, `get_top_locations`
- `get_stats_table`, `get_stats_graph`, `get_time_graph_data`
- `get_gps_graph_data`, `get_gps_scan_map_data`
- `get_murl_stats` with `murlId`, `get_bulk_stats` with `bulkId`
- `get_vcard_contacts_from_qr` for contacts collected by a vCard QR
- `export_qr_data` needs `qrType` and `qrId`
- `reset_qr_data` clears scan data after confirmation. Use it only when the user explicitly wants the scan history removed.

## Ownership

- `get_change_owner_requests` lists pending transfers.
- `request_change_owner` starts a transfer. Pass the request body in `payload`.
- `process_change_owner_request` accepts or rejects a request after confirmation.
