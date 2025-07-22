```verilog
module AdderSubtractorNbit #(parameter N=4) (input wire [N-1:0] a,b,
                                             input wire cin, ctrl,
                                             output wire [N-1:0]s, 
                                             output wire cout);
  wire [N:0] w;
  wire [N-1:0]bc;
  assign w[0]=cin;
  assign cout=w[N];
  assign bc = b ^ {N{ctrl}};
  
  
  genvar i;
  generate
  for(i=0;i<N;i=i+1)begin
    full_adder fa(.a(a[i]),.b(bc[i]),.cin(w[i]),.s(s[i]),.cout(w[i+1]));
  end
  endgenerate
endmodule




module full_adder (input wire a,b,cin,
                   output wire s,cout);
  wire [2:0]w;
  
  half_adder ha0 (.a(a),.b(b),.s(w[1]),.cout(w[0]));
  half_adder ha1 (.a(w[1]),.b(cin),.s(s),.cout(w[2]));
  
  assign cout= w[0]|w[2];
endmodule


module half_adder ( input wire a,b,
                   output wire s,cout);
  assign s= a^b;
  assign cout= a&b;
endmodule
