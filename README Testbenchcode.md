```verilog
`timescale 1ps/1ps

module tbAdderSubtractorNbit;
  parameter N=4;
  reg [N-1:0] a,b;
  reg cin, ctrl;
  reg [N-1:0]bc;
  wire [N-1:0]s;
  wire cout;
  reg [N:0]exp_s;
  reg exp_c;
  integer j,k;
  integer err=0;
  
  AdderSubtractorNbit #(N) DUT(a,b,cin,ctrl,s,cout);
  
  initial begin
    $monitor ("time=%0t,a=%b,b=%b,bc=%b,cin=%b,ctrl=%b,s=%b,cout=%b | exp_s=%b,exp_c=%b",$time,a,b,bc,cin,ctrl,s,cout,exp_s,exp_c);
    
    for(ctrl=0;ctrl<2;ctrl=ctrl+1)begin
    for(j=0;j<2**N;j=j+1)begin
      for(k=0;k<2**N;k=k+1)begin
          a=j;
          b=k;
          cin=ctrl;
          bc = b ^ {N{ctrl}};
          exp_s = a+bc+cin;
          exp_c = exp_s[N];
          #5
          check(s,cout,exp_s[N-1:0],exp_c);
        end
     end
   end
    
    if(err==0)begin
      $display("-----------");
      $display("Test Pass");
      $display("-----------");
    end else begin
      $display("-----------");
      $display("Test False with %d erorr",err);
      $display("-----------");
    end
    
    $finish;
  end
  
  
  task check(input [N-1:0]s,input cout,input [N-1:0]exp_s,input exp_c);
    begin 
      if(exp_s!==s || exp_c!==cout) begin
        $display ("[CHECK]:ERROR");
        err=err+1;
      end else begin
        $display ("[CHECK]:MATCHING");
        end
    end
  endtask
  
  initial begin 
    $dumpfile("dump.vcd");
    $dumpvars;
 end
endmodule
  
