## Group Pickup Feature

### About Group Pickup Feature

Feature code to toggle what group (group, user, device, extension) to pick up a ringing call from.

#### Schema

Validator for the group_pickup_feature callflow data object



Key | Description | Type | Default | Required | Support Level
--- | ----------- | ---- | ------- | -------- | -------------
`approved_device_id` | Restrict calling device to Device | `string()` |   | `false` |  
`approved_group_id` | Restrict calling device to Group | `string()` |   | `false` |  
`approved_user_id` | Restrict calling device to User | `string()` |   | `false` |  
`device_id` | Device to pickup | `string()` |   | `false` |  
`group_id` | Group in which to find a call to pickup | `string()` |   | `false` |  
`skip_module` | When set to true this callflow action is skipped, advancing to the wildcard branch (if any) | `boolean()` |   | `false` |  
`type` | The type of collection to pickup | `string('group' | 'user' | 'device' | 'extension')` | `extension` | `true` |  
`user_id` | User in which to find a call to pickup | `string()` |   | `false` |  






### Usage

The feature code for group pickup will lookup a device by SIP username or an extension. The callflow for that extension is searched in a depth-first order until a target-able module is found (one of `user`, `device`, `ring_group`, or `page_group`). Once a suitable module is found, if there is an unanswered call ringing a targeted device it will be picked up.

#### Extension callflow

Create a callflow with `patterns' key as: `["^\\*7([0-9]*)$"]` and set the parameter `type' to `extension`.

```json
{"patterns":["^\\*7([0-9]+)$"]
 ,"flow":{
   "module":"group_pickup_feature"
   ,"data":{"type":"extension"}
 }
}
```

Now create a callflow to ring appropriate endpoint(s) (say extension 401).

Dial `401` to start ringing the endpoints; on another phone, dial `*7401` to pickup the call.

#### SIP Username callflow

This example was done using BLF on a `spa504g`.

The SIP username of the device we want to monitor for this example is `55578547`.

Connect to the phone's UI and on the `Phone` Tab, go to `Line Key 2` and set:

Setting|Value
Extension|disabled
Share Call Appearance|private
Extended Function|`:fnc=blf+cp;sub=55578547@sip.domain.com;ext=55578547@sip.domain.com`

On the `Attendant Console` Tab, set `Attendant Console Call Pickup Code` to `*98#`. This way the user name part of the subscription is passed along (`*9855578547`).

Create the feature code callflow:

```json
{"patterns":["^\\*98([0-9]*)$"]
 ,"flow":{
   "module":"group_pickup_feature"
   ,"data":{"type":"device"}
 }
}
```
