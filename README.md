# esp-msgpack

Message Pack components for ESP32 (esp-idf).

## Usage

1. clone or submodule this repo to `components/msgpack`.
2. add `CFLAGS += -DMSGPACK_ENDIAN_LITTLE_BYTE` to your `main/component.mk`.
3. use!

## Example app

```c
#include <stdio.h>
#include <stdint.h>

#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

#include "msgpack.h"

void app_main() {
    msgpack_sbuffer* buffer = msgpack_sbuffer_new();
    msgpack_packer* pk = msgpack_packer_new(buffer, msgpack_sbuffer_write);

    /* serializes ["Hello", "MessagePack", 12345]. */
    msgpack_pack_array(pk, 3);
    msgpack_pack_bin(pk, 5);
    msgpack_pack_bin_body(pk, "Hello", 5);
    msgpack_pack_bin(pk, 11);
    msgpack_pack_bin_body(pk, "MessagePack", 11);
    msgpack_pack_int(pk, 12345);

    /* deserializes it. */
    msgpack_unpacked msg;
    msgpack_unpacked_init(&msg);
    msgpack_unpack_return ret = msgpack_unpack_next(&msg, buffer->data, buffer->size, NULL);

    /* prints the deserialized object. */
    msgpack_object obj = msg.data;
    msgpack_object_print(stdout, obj);  /*=> ["Hello", "MessagePack", 12345] */

    /* cleaning */
    msgpack_sbuffer_free(buffer);
    msgpack_packer_free(pk);

    vTaskDelete(NULL);
}
```

## LICENSE

MIT

