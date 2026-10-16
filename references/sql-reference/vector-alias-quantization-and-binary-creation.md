# Vector alias quantization and binary creation

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Continue](vector-cosine-and-euclidean-distance.md)

### vector

 Builds a vector with the default representation, which is 32-bit float.

```
vector(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | A JSON array of numbers |

 **Returns:** BLOB - a vector encoded in the default binary representation, 32-bit float.

```
SELECT vector('[1.0, 2.0, 3.0]');

```

### vector8

 Builds an 8-bit quantized vector from a JSON numeric array.

```
vector8(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON array containing integer values from 0-255 |

 **Returns:** BLOB - a vector encoded as 8-bit integer binary data. Use it for approximate vectors when compact storage is important. Each dimension takes 1 byte.

```
SELECT vector8('[128, 64, 255, 0]');

```

### vector1bit

 Builds a 1-bit binary vector from a JSON array.

```
vector1bit(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON array containing 0s and 1s |

 **Returns:** BLOB - a vector encoded in 1-bit binary form. Use it when the representation must be extremely compact. A single byte stores 8 dimensions.

```
SELECT vector1bit('[1, 0, 1, 1, 0, 0, 1, 0]');

```

---

### Distance Functions

 Distance functions measure how far apart two vectors are. Lower results mean the vectors are more alike.
