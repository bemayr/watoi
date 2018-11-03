# Database documentation for WhatsApp
![apple|2.18.100](https://img.shields.io/badge/-2.18.100-blue.svg?logo=apple)

> In this document I try to reverse engineer the database used by WhatsApp on iOS to enable cross-messenger message transfers.

## How to get the DB for reverse-engineering
Take a look at step 3 of the [README's step-by-step-guide](https://github.com/bemayr/watoi/blob/master/README.md#step-by-step-guide) and open the DB with the sqlite-viewer of your choice.

## Legend
- 🔑 is a primary key column
- 🔗 is a foreign key column
- 🔜 should be coming soon
- ❓ is unknown
- 🧐 needs further investigation

## General
### `[...]JID`
These columns represent the contacts. The format is like `<phone-number>@<domain>` where
- `<phone-number>` does not contain any special characters or prefixed 0s, just the number and
- `<domain>` is either
  - `s.whatsapp.net`: *normal contact*
  - `g.us`: *group address*
  - `status`: *story contacts*

### `Z_ENT`
It looks like this is some kind of "table index". It starts at 1 (`ZWABLACKLISTITEM`) and e.g. `ZWAMEDIAITEM` has 8.

### `Z_OPT`
🧐

### Datatype: *integer.bool*
- 0 = `false`
- 1 = `true`

### Datatype: timestamp
The Apple NSDate class calculates time relative to *00:00:00 UTC on 1 January 2001*. [docs](https://developer.apple.com/documentation/foundation/nsdate#//apple_ref/occ/instm/NSDate/timeIntervalSince1970)


## Tables
### `ZWABLACKLISTITEM`
Blocked contacts; This information must be stored on the WhatsApp servers, because it was automatically restored in my case.
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZJID`, _varchar_: [contact id](#jid)

### `ZWACHATPROPERTIES`
This table is empty in my backup. 🧐

### `ZWACHATPUSHCONFIG`
🔜

### `ZWACHATSESSION`
List of all the conversations.
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZARCHIVED`, _integer.bool_: whether this chat is archived
- `ZCONTACTABID`, _integer_: ❓
- `ZFLAGS`, _integer_: ❓ a lot of groups have 1280, single chats 1296 and hidden single chats 1304, but there are exceptions 🧐
  - 1280 = ❓
  - 1296 = ❓
  - 1304 = ❓
- `ZHIDDEN`, _integer.bool_: whether this chat is hidden from the chat list view (if no message was sent, but the profile was opened or the user looked at the contact's story)
- `ZIDENTITYVERIFICATIONEPOCH`, _integer_: always 0 🧐
- `ZIDENTITYVERIFICATIONSTATE`, _integer_: always 0 🧐
- `ZMESSAGECOUNTER`, _integer_: number of messages in this chat (start index 🧐)
- `ZREMOVED`, _integer.bool_: whether this chat was removed
- `ZSESSIONTYPE`, _integer_: type of the chat
  - 1 = group chat
  - 0 = single chat
  - 3 = story
- `ZSPOTLIGHTSTATUS`, _integer_: ❓
- `ZUNREADCOUNT`, _integer_: number of unread messages
- `ZGROUPINFO` 🔗, _integer_: foreign key to the [group info](#zwagroupinfo)
- `ZLASTMESSAGE` 🔗, _integer_: foreign key to the [last received message](#zwamessage)
- `ZPROPERTIES`, _integer_: always `null` in my case 🧐
- `ZLASTMESSAGEDATE`, _timestamp_: timestamp of the last message
- `ZLOCATIONSHARINGENDDATE`, _timestamp_: always `null` in my case 🧐
- `ZCONTACTIDENTIFIER`, _varchar_: ❓
  - `null` for group chats
  - `guid` for single chats, I've got one weird `:ABPerson`-suffix for one contact 🧐
- `ZCONTACTJID`, _varchar_: [contact id](#jid)
- `ZETAG`, _varchar_: ❓
  - `w:306184;` is the value in one specific chat 🧐
- `ZLASTMESSAGETEXT`, _varchar_: always `null` in my case 🧐
- `ZPARTNERNAME`, _varchar_: name of the contact as set in the app itself
- `ZSAVEDINPUT`, _varchar_: always `null` in my case 🧐

### `ZWAGROUPINFO`
🔜

### `ZWAGROUPMEMBER`
🔜

### `ZWAGROUPMEMBERSCHANGE`
🔜

### `ZWAMEDIAITEM`
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZCLOUDSTATUS`, _integer_:
- `ZFILESIZE`, _integer_:
- `ZMEDIAORIGIN`, _integer_:
- `ZMOVIEDURATION`, _integer_:
- `ZMESSAGE`, _integer_:
- `ZASPECTRATIO`, _float_:
- `ZHACCURACY`, _float_:
- `ZLATITUDE`, _float_:
- `ZLONGITUDE`, _float_:
- `ZMEDIAURLDATE`, _timestamp_:
- `ZAUTHORNAME`, _varchar_:
- `ZCOLLECTIONNAME`, _varchar_:
- `ZMEDIALOCALPATH`, _varchar_:
- `ZMEDIAURL`, _varchar_:
- `ZTHUMBNAILLOCALPATH`, _varchar_:
- `ZTITLE`, _varchar_:
- `ZVCARDNAME`, _varchar_:
- `ZVCARDSTRING`, _varchar_:
- `ZXMPPTHUMBPATH`, _varchar_:
- `ZMEDIAKEY`, _blob_:
- `ZMETADATA`, _blob_:

### `ZWAMESSAGE`
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZCHILDMESSAGESDELIVEREDCOUNT`, _integer_:
- `ZCHILDMESSAGESPLAYEDCOUNT`, _integer_:
- `ZCHILDMESSAGESREADCOUNT`, _integer_:
- `ZDATAITEMVERSION`, _integer_:
- `ZDOCID`, _integer_:
- `ZENCRETRYCOUNT`, _integer_:
- `ZFILTEREDRECIPIENTCOUNT`, _integer_:
- `ZFLAGS`, _integer_:
- `ZGROUPEVENTTYPE`, _integer_:
- `ZISFROMME`, _integer_:
- `ZMESSAGEERRORSTATUS`, _integer_:
- `ZMESSAGESTATUS`, _integer_:
- `ZMESSAGETYPE`, _integer_:
- `ZSORT`, _integer_:
- `ZSPOTLIGHTSTATUS`, _integer_:
- `ZSTARRED`, _integer_:
- `ZCHATSESSION`, _integer_:
- `ZGROUPMEMBER`, _integer_:
- `ZLASTSESSION`, _integer_:
- `ZMEDIAITEM`, _integer_:
- `ZMESSAGEINFO`, _integer_:
- `ZPARENTMESSAGE`, _integer_:
- `ZMESSAGEDATE`, _timestamp_:
- `ZSENTDATE`, _timestamp_:
- `ZFROMJID`, _varchar_:
- `ZMEDIASECTIONID`, _varchar_:
- `ZPHASH`, _varchar_:
- `ZPUSHNAME`, _varchar_:
- `ZSTANZAID`, _varchar_:
- `ZTEXT`, _varchar_:
- `ZTOJID`, _varchar_:

### `ZWAMESSAGEDATAITEM`
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZINDEX`, _integer_:
- `ZOWNSTHUMBNAIL`, _integer_:
- `ZTYPE`, _integer_:
- `ZMESSAGE`, _integer_:
- `ZDATE`, _timestamp_:
- `ZCHATJID`, _varchar_:
- `ZCONTENT1`, _varchar_:
- `ZCONTENT2`, _varchar_:
- `ZMATCHEDTEXT`, _varchar_:
- `ZSECTIONID`, _varchar_:
- `ZSENDERJID`, _varchar_:
- `ZSUMMARY`, _varchar_:
- `ZTHUMBNAILPATH`, _varchar_:
- `ZTITLE`, _varchar_:

### `ZWAMESSAGEINFO`
- `Z_PK` 🔑, _integer_
- `Z_ENT`, _integer_
- `Z_OPT`, _integer_
- `ZMESSAGE`, _integer_:
- `ZRECEIPTINFO`, _blob_:

### `ZWAPROFILEPICTUREITEM`
🔜

### `ZWAPROFILEPUSHNAME`
🔜

### `ZWAVCARDMENTION`
🔜

### `ZWAZ1PAYMENTTRANSACTION`
🔜

### `Z_METADATA`
🔜

### `Z_MODELCACHE`
🔜

### `Z_PRIMARYKEY`
🔜
