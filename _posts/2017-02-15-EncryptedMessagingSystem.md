---
layout:notes
title: Encrypted Messaging Systems 
scribe: Yingqiao Xiong
---
##'end to end encryption'
+ There are three types of encryption: 1. Encryption in Transit 2. end to end encryption 3. end to end encrption(no service provider)
1. Encryption in Transit:

Alice -----encrypted------ service provider (no encrption) ------encrypted------ Bob

2. end to end encryption:

Alice -----encrypted------ service provider(encrypted) ------encrypted------ Bob

3. end to end encrption(no service provider)

Alice -----encrypted------ encrypted ------encrypted------ Bob

##the OTR 3-step Diffie-Hellman ratchet
      
          Alice                                             Bob
            g^a
    [ encrypted message ]            ->       
    [ next key advertisement ]                 
                                                            g^b
                                                   [ encrypted message ]
                                       <-          [ Key Acknowledge ]
                                                   [ next key advertisement ]
             g^c                                      
    [ encrypted message ]
    [ Key Acknowledge ]                ->
    [ next key advertisement ]
                                                         ( k1, k2 ) = g^ab
                                                         ciphertext = ENCk1(msg)
                                                         ack = MACk2(something)
    
    
##Asyn hash ratchet

+ K1 = H(m)
+	K2 = H(K1)
+	K3 = H(K2)
+That key material is sensitive, however, because it can be used to calculate the key material for every subsequent sequence number. So a client can't hang on to it forever

##Signal's 2-Step DH Ratchet ("Chain Keys")

           Alice                                         Bob
        [ Encrypted Message ]                   
        [ Current Ephememeral key ]     ->

                                                  [ Encrypted Message ]                   
                                          <-      [ Current Ephememeral key ]
                                     
+1. Alice generates a new ECDH ephemeral key `A1` and uses it immediately to send a message.
+2. Alice receives a message with Bob's new ECDH ephemeral `B1` and can then destroy `A1` and generate `A2` when sending her next message. 

## Meta Data
+ leaks of metadata can lead to great troubles.
+ NSA said "we kill people based on metadata"



# Anonymous Communication with emphasis on TOR
     ------------------
     |  -------------  |
     |  |  --------- | |    
     |  |  |MESSAGE| | | 
     |  |  --------- | |   ciphertext = Epk1 [Epk2[Epk3[message]]]
     |  -------------  |
      -----------------


      Sever 1                                Sever 2                           Sever 3

      ------------------              
     |  -------------  |                  ------------
     |  |  --------- | |                  | -------- |                       ------- 
     |  |  |   M1  | | |                  | |   M2 | |                       |   M1|                       M2
     |  |  --------- | |                  | -------- |                       -------
     |  -------------  |                  ------------
      -----------------
      
      ------------------
     |  -------------  |                  -----------
     |  |  --------- | |                 |   ------- |      Decrypt
     |  |  |  M2   | | | Decrypt and     |   |  M3 | |      and               ------      D and p
     |  |  --------- | | permutate       |   ------- |      permutate         | M2 |     ---->             M3
     |  -------------  | --------------> -------------      --------->        ------
      -----------------
      ------------------
     |  -------------  |                 --------------
     |  |  --------- | |                |    --------  |                      -----
     |  |  |   M3  | | |                |    |   M1 |  |                     | M3 |                        M1
     |  |  --------- | |                |   --------   |                      ------
     |  -------------  |                ----------------
      ----------------- 
