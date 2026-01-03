# Chess Engine in Kotlin and Go

For my master course "Concepts of Programming Languages" I implemented a chess engine in two 
languages (Kotlin and Go) to present the core concepts of object-oriented programming and each
language's suitability to it.

# Project Content
## Builds
The Builds folder has the finished builds of the two chess engines, marked by their respective languages.
To run the Kotlin build, Java needs to be installed (tested with Java 25), then open the command terminal in the same directory as the file and run the file
via
> **java -jar ChessEngine_Kotlin.jar**

For the Go build, it is recommended to run it via the command terminal as well by also opening the terminal
in the same directory and typing "ChessEngine_Go.exe". It is possible to open it directly, and it will work 
but a checkmate will terminate the program before being able to see the result.

Note: the builds have letter shortcuts for the chess pieces since that is most friendly for all types of terminals. 
However, within the Kotlin implementation there is a unicode implemented rendering that used unicode characters which
are much nicer to look at.

## Projects
Both source codes are marked by their respective languages. The Kotlin implementation was developed in Intellij
IDEA, whereas the Go implementation was done in Goland.
To open the Kotlin implementation, open the project folder (chessEngine_kotlin) in Intellij IDEA and configure it to Java when asked.
To open the Go implementation, open the project folder (chessEngine_go) in Goland and setup GOROOT (tested for Go 1.25.1).

### Testing the Code
Both implementation include unit tests that can be run in their respective IDEs. In Kotlin, these can be found in the folder **test**.
However, there was a problem with the version that was being used in conjunction the mocking libraries that I had attempted to use 
(Mocck, Mockito). As such, there is a homebrewed MockBoardStateManager which is used for testing instead. The Kotlin implementation tests the following:
- Board rendering based on bitboards
- Board state managing in case of check and checkmate
- Chess piece movement for move and attack determination (multistep and single-step)
- Input handling

In Go, the movement and state packages have tests which are mostly mirrors of the Kotlin ones. They can be found in the 
**piece/movement** and **state** folders.

# Implementation
## Code Structure
The implementation followed a German guideline (which can be found [here](https://computerchess.com/Schach-lernen/Schachregeln-einfach-erklaert/))
However, not every rule was implemented. This chess engine implements the following:
- Basic Rook, Knight, Bishop and Queen movement
- Basic King movement with castling
- Basic Pawn movement with en passant capture and promotion
- Check and Checkmate

For the management of the boards, bitboards were used which can be read up on [here](https://www.chessprogramming.org/Bitboards)  
(chess programming wiki) and [here](https://johns.codes/blog/making-a-chess-engine-in-zig) (chess engine in Zig by John Murray).

Essentially, 64 bits (ULong in Kotlin, uint64 in Go) were used to represent each square on a chess board. If there is chess piece on a
square, the bit is flipped on. To get a representation of the board, twelve bitboards are used; one for each chess piece in each color.
Moves and attacks can then simply be calculated via bit operations.

As for the chess pieces themselves, in the Kotlin version a base class was implemented (**ChessPiece**). This class assumes the default piece to be white and infinitely
move in each direction according to its movement pattern. **SingleStep** overrides the function responsible for finding moves and attacks to be
suitable for all pieces that only move one step in each direction (always according to its movement pattern).

Furthermore, the King and Pawn pieces are implemented separately to account for the castling move (king) and the en passant capture/ promotion (pawn).

[![](https://mermaid.ink/img/pako:eNqVVNFumzAU_RV0n1aVRKQhZlh7qdo8VZXQsqeNVXLhBqyCjWzTNcvy77OhTUJCp84P5vqcc22fI2ALmcwRKGQV0_qWs0KxOhWeHTclap1wzND78mcy8VZcFBWuDDZnPPUul131bdOg17hqTPPjJxfGq-UzJswYVOJUZDUZE8sXzFqD91b36WJEUqDpSidYoRnVrLnIHa_fZa-NYdnTP_iqSqTW_LHCvdTrtf18yKPP586ux5mE_bJWe67L-Uix7WE3Hsa974at7pijpomSWcnyQUeH61Iq87UnB0QlRTGCP5xFtkdPojrgIxF9xM7BjEvm2EzT6rLPZnBlB9_K9vEERpHYTZgwZ-YrXJvlGztgFC_Kdyj7Xi0F1ht3qQEhpOHrTaJkbQsp_iOHHfhQKJ4DNapFH2pUNXNL6FynYEqsMQVqy5yppxRS4XoaJr5LWb-1KdkWJdA1q7RdtU3ODL5-q3tUochR3chWGKBXUTDrdgG6hReg82A2ncUxCRd2CuJ5tPBhAzQm01kYBldB8DlYEBLufPjdHRtMSbQICYnDiITziETEB8y5ker-9XfhHru_9NFOHw?type=png)](https://mermaid.live/edit#pako:eNqVVNFumzAU_RV0n1aVRKQhZlh7qdo8VZXQsqeNVXLhBqyCjWzTNcvy77OhTUJCp84P5vqcc22fI2ALmcwRKGQV0_qWs0KxOhWeHTclap1wzND78mcy8VZcFBWuDDZnPPUul131bdOg17hqTPPjJxfGq-UzJswYVOJUZDUZE8sXzFqD91b36WJEUqDpSidYoRnVrLnIHa_fZa-NYdnTP_iqSqTW_LHCvdTrtf18yKPP586ux5mE_bJWe67L-Uix7WE3Hsa974at7pijpomSWcnyQUeH61Iq87UnB0QlRTGCP5xFtkdPojrgIxF9xM7BjEvm2EzT6rLPZnBlB9_K9vEERpHYTZgwZ-YrXJvlGztgFC_Kdyj7Xi0F1ht3qQEhpOHrTaJkbQsp_iOHHfhQKJ4DNapFH2pUNXNL6FynYEqsMQVqy5yppxRS4XoaJr5LWb-1KdkWJdA1q7RdtU3ODL5-q3tUochR3chWGKBXUTDrdgG6hReg82A2ncUxCRd2CuJ5tPBhAzQm01kYBldB8DlYEBLufPjdHRtMSbQICYnDiITziETEB8y5ker-9XfhHru_9NFOHw)

In the Go version, a Mover interface was instead implemented which included all base function that are present in ChessPiece. This interface was then separately
implemented for multistep (Rook, Bishop, Queen) single-step (Knight, King) and Pawn pieces. Since polymorphism was achieved through the Mover interface 
(which must be implemented fully to be functional), it was not possible to avoid the code duplication of the CanExecuteMove() function.

It should be noted here that the Go implementation is **heavily based** on the Kotlin one, with the code being transposed from one into the other and rewritten
to be in the right syntax to evaluate the viability of an OOP approach in Go.

Lastly, as for the structure itself, there are five major components: input handling, board state managing, board rendering, 
chess piece movement constraints and the overall management of the game loop ("game managing").

## Program Flow
The core loop of the chess engine is fairly simple. The GameManager responsible for the game loop requests input from the InputHandler, which
sends back a response in the correct format regardless of its content. This is then evaluated by the BoardStateManager in terms of square occupation
(is there a piece on that square?) and movement execution (can this chess piece execute that move?) before executing it.

A response is once again sent back and evaluated by the GameManager for any needed cleanup before it requests more input.

[![](https://mermaid.ink/img/pako:eNp1UslOwzAQ_RVrriRVFpzFhx4ACZDoiRvKxaqHNCK2g2OjQtV_x27aUKjwaeb5LaOxd7DWAoHBiO8O1RrvOt4aLhtF_FnxTsXL5dU9l7jiirdoGBktN5a0HiK91sPEDBUJtKcZOhMFj0c1OPvAleiDiQlxoyVdQCf-OSH2ivh3KipBtLMz_Y_9jeZGPFtufySnDKk_kOAW1852Wk3qC_o_ibPMu42uP2b7m8shgkFYGJt24zkoIILWdAKYNQ4jkGgkDy3sgr4Bu0GJDTBfCm7eGmjU3msGrl60lieZ0a7dAHvl_eg7Nwg_9vGdZtSEPHOrnbLA0jI_mADbwRZYRukiyao0L7OkTPI0oxF8elZSLqqK0oQWZZEVNc33EXwdcpNF6eEqK4rruq7zIvV-KDqrzWr6Lodfs_8GPpi6rQ?type=png)](https://mermaid.live/edit#pako:eNp1UslOwzAQ_RVrriRVFpzFhx4ACZDoiRvKxaqHNCK2g2OjQtV_x27aUKjwaeb5LaOxd7DWAoHBiO8O1RrvOt4aLhtF_FnxTsXL5dU9l7jiirdoGBktN5a0HiK91sPEDBUJtKcZOhMFj0c1OPvAleiDiQlxoyVdQCf-OSH2ivh3KipBtLMz_Y_9jeZGPFtufySnDKk_kOAW1852Wk3qC_o_ibPMu42uP2b7m8shgkFYGJt24zkoIILWdAKYNQ4jkGgkDy3sgr4Bu0GJDTBfCm7eGmjU3msGrl60lieZ0a7dAHvl_eg7Nwg_9vGdZtSEPHOrnbLA0jI_mADbwRZYRukiyao0L7OkTPI0oxF8elZSLqqK0oQWZZEVNc33EXwdcpNF6eEqK4rruq7zIvV-KDqrzWr6Lodfs_8GPpi6rQ)

This concludes a rough overview of the chess engine implementation.

### Note: BoardRenderer and CmdBoardRenderer
Within the Kotlin implementation, there are two renderers for the board with the Cmd prefixed one being used in the build due to its terminal friendliness.
However, this renderer can be replaced in the GameManager class with the normal BoardRenderer (just delete the Cmd) which uses unicode emojis for the chess pieces.

To make sure, that the unicode is displayed correctly, configure the VM option within the Run Configurations to -Dstdout.encoding=UTF8.

# Sources
There are multiple websites which heavily inspired the approach and implementation to this chess engine or which helped during
its development in cases of debugging and testing:

[BitBoard Calculator](https://gekomad.github.io/Cinnamon/BitboardCalculator/) by gekomad

[Bitboards](https://www.chessprogramming.org/Bitboards#General_Bitboard_Techniques) by ChessProgramming Wiki

[Kotlin Unit Testing guide](https://medium.com/@kacper.wojciechowski/kotlin-unit-testing-guide-part-1-introduction-and-basics-c190c66988e9) by Kacper Wojciechowski

[Making a Chess Engine in Zig](https://johns.codes/blog/making-a-chess-engine-in-zig) by John Murray

[Visualizing Chess Bitboards](https://healeycodes.com/visualizing-chess-bitboards) by Andrew Healey

[What is Bitmasking?](https://www.geeksforgeeks.org/dsa/what-is-bitmasking/) by GeeksforGeeks
