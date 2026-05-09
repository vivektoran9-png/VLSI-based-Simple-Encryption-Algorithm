# VLSI-based-Simple-Encryption-Algorithm

// ==========================================================
// VLSI XOR ENCRYPTION/DECRYPTION MODULE
// ==========================================================
module xor_codec(
    input [7:0] data_in,   // Input (Plaintext or Ciphertext)
    input [7:0] secret_key,// 8-bit Encryption Key
    input enable,          // Enable processing
    output reg [7:0] data_out // Output (Ciphertext or Decrypted text)
);
    always @(*) begin
        if (enable)
            data_out = data_in ^ secret_key; // Bitwise XOR
        else
            data_out = 8'bz; // High impedance if disabled
    end
endmodule

// ==========================================================
// TESTBENCH
// ==========================================================
module encryption_tb;
    reg [7:0] plaintext;
    reg [7:0] key;
    reg en;
    wire [7:0] ciphertext;
    wire [7:0] decrypted_text;

    // Instance 1: Encryption
    xor_codec encryptor (
        .data_in(plaintext),
        .secret_key(key),
        .enable(en),
        .data_out(ciphertext)
    );

    // Instance 2: Decryption (Feeding ciphertext back in)
    xor_codec decryptor (
        .data_in(ciphertext),
        .secret_key(key),
        .enable(en),
        .data_out(decrypted_text)
    );

    initial begin
        // Initialize
        en = 1;
        key = 8'b1010_0110;       // Secret Key: 0xA6
        plaintext = 8'b0100_1101; // Data: 0x4D ('M')

        #10;
        $display("--- Encryption Simulation ---");
        $display("Original Plaintext: %b (Hex: %h)", plaintext, plaintext);
        $display("Secret Key:         %b (Hex: %h)", key, key);
        $display("Generated Cipher:   %b (Hex: %h)", ciphertext, ciphertext);
        
        #10;
        $display("\n--- Decryption Simulation ---");
        $display("Cipher Input:       %b", ciphertext);
        $display("Decrypted Result:   %b (Hex: %h)", decrypted_text, decrypted_text);
        
        if (plaintext == decrypted_text)
            $display("\nSUCCESS: Decrypted data matches original!");
        else
            $display("\nERROR: Mismatch detected.");
            
        $finish;
    end
endmodule
