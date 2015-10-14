### Vendor Request
APBridgeA currently support the following requests:
* APBRIDGE_RWREQUEST_SVC             (0x01)
* APBRIDGE_RWREQUEST_LOG             (0x02)
* APBRIDGE_RWREQUEST_EP_MAPPING      (0x03)
* APBRIDGE_ROREQUEST_CPORT_COUNT     (0x04)
* APBRIDGE_WOREQUEST_CPORT_RESET     (0x05)
* APBRIDGE_ROREQUEST_LATENCY_TAG_EN  (0x06)
* APBRIDGE_ROREQUEST_LATENCY_TAG_DIS (0x07)

### APBRIDGE_RWREQUEST_SVC
This request is used to send data to SVC.
* bmRequestType = 1b010xxxxx
* bRequest = 0x01
* wLength define the size data to send to SVC
* wValue and wIndex are not used

### APBRIDGE_RWREQUEST_LOG
This request is used to get logs on APBridgeA.
This is only useful if APBridgeA does not have a serial console.
* bmRequestType = 1b110xxxxx
* bRequest = 0x02
* wLength define the size logs to get from APBridgeA
* wValue and wIndex are not used

### APBRIDGE_RWREQUEST_EP_MAPPING
This request is used to map a cport to a particular set of in and out endpoints.
* bmRequestType = 1b010xxxxx
* bRequest = 0x03
* wLength unused
* wValue - contains a struct cport_to_ep data value
* wIndex are not used

struct cport_to_ep {
        __le16 cport_id;
        __u8 endpoint_in;
        __u8 endpoint_out;
};

This should be useful for QoS. Instead of muxing cports traffic on one set of endpoints,
use one set of endpoints for one cport that require low latency or high bandwidth.
```
cport x => ep y (in) => USB => AP
AP => USB => ep z (out) => cport x
```
### APBRIDGE_ROREQUEST_CPORT_COUNT
This command is used to return the number of cports available to the AP in the wValue field
of the control request.
* bmRequestType = 1b110xxxxx
* bRequest = 0x04
* wLength = 2 representing the number of bytes to return in the data phase of the command
* wValue = device returns the cport count in this parameter
* wIndex not used

### APBRIDGE_WOREQUEST_CPORT_RESET
This command is currently not supported fully between AP and APBridge
Consider this command bRequest reserved.
* bmRequestType = 1b010xxxxx
* bRequest = 0x05
* wLength = not used
* wValue = cport_id to reset
* wIndex not used

### APBRIDGE_ROREQUEST_LATENCY_TAG_EN
This request is used to enable timestamp tracking for the specified CPort.
* bmRequestType = 1b010xxxxx
* bRequest = 0x06
* wLength = 0
* wValue = cport_id
* wIndex unused

### APBRIDGE_ROREQUEST_LATENCY_TAG_DIS
This request is used to disable timestamp tracking for the specified CPort.
* bmRequestType = 1b010xxxxx
* bRequest = 0x07
* wLength = 0
* wValue = cport_id
* wIndex unused

ES1 only have one set of endpoints available then this request is only available on ES2.
* bmRequestType = 1b010xxxxx
* bRequest = 0x03
* wLength = 0x04
* wValue and wIndex are not used

Data format:
```
struct cport_to_ep {
	__le16 cport_id;   //id of cport to map
	__u8 endpoint_in;  //id of in endpoint
	__u8 endpoint_out; //id of out endpoint 
};
```

* cport_id: any cport between 0 and 44 except 16 and 17 reserved for cdsi
* endpoint_in: any odd number between 3 and 15
* endpoint_out: any even number between 2 and 14

#### Known limitations:
* by default, all cports are muxed on endpoints 2 and 3
* cports can not be muxed on another endpoints (legacy, I will remove this limitation soon)
