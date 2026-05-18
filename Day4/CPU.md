##  Program Counter
### Each instruction in this CPU is 32 bits (4 bytes) long.
### So one instruction occupies 4 memory locations because each location stores 1 byte.
```bash
 |pc
      @0
         $reset = *reset;

         $inc_pc[31:0] = >>1$pc[31:0] + 32'd4;

         $pc[31:0] = >>1$reset ? 32'd0 :
                                      $inc_pc[31:0];

```
## Result:
<img width="1917" height="823" alt="image" src="https://github.com/user-attachments/assets/a59a53e3-88ac-4cc0-8e08-bb89a44e4030" />

