
## Calculator with counter
```bash
\m5_TLV_version 1d: tl-x.org
\m5
   
   // ============================================
   // Welcome, new visitors! Try the "Learn" menu.
   // ============================================
   
   //use(m5-1.0)   /// uncomment to use M5 macro library.
\SV
   // Macro providing required top-level module definition, random
   // stimulus support, and Verilator config.
   m5_makerchip_module   // (Expanded in Nav-TLV pane.)
\TLV
   // Connect SV inputs to TLV pipesignals.
    
    
      $cnt[4:0] =reset ? (1+ >>1$cnt) : 1;
    
   $reset = *reset; 
    $val1[31:0] = (>>1$out[31:0]);
   $val2[31:0] = $rand2[3:0];
   $sum[31:0] = $val1[31:0] + $val2[31:0];
   $diff[31:0] = $val1[31:0] - $val2[31:0];
   $prod[31:0] = $val1[31:0] * $val2[31:0];
   $quot[31:0] = $val1[31:0] / $val2[31:0];
   $out[31:0] = ($op[1:0] == 2'b00)
                        ? $sum[31:0] : 
                       ($op[1:0] == 2'b01)   
                        ? $diff[31:0] :
                       ($op[1:0] == 2'b10)  
                        ? $prod[31:0] :
                         $quot ;
            $out [31:0] = reset ? 0 : $out[31:0];
       
      
         
   //...
   
   // Assert these to end simulation (before the cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
\SV
   endmodule




```
## Calculator with two stage
```bash
 |calc
   @1
      $reset = *reset;
      $val1[31:0] = (>>1$out[31:0]);
      $val2[31:0] = $rand2[3:0];
      $sum[31:0] = $val1[31:0] + $val2[31:0];
      $diff[31:0] = $val1[31:0] - $val2[31:0];

      $prod[31:0] = $val1[31:0] * $val2[31:0];
      $quot[31:0] = $val1[31:0] / $val2[31:0];
      $valid[4:0] =$reset ? (1+ >>1$valid) : 1;
       
   @2
         $out[31:0] = ($op[1:0] == 2'b00)
                        ? $sum[31:0] : 
                       ($op[1:0] == 2'b01)   
                        ? $diff[31:0] :
                       ($op[1:0] == 2'b10)  
                        ? $prod[31:0] :
                         $quot ;
            $out[31:0] = ($reset || !$valid) ? 0 : $out[31:0];
```
