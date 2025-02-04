import java.util.Random;
import java.util.Scanner;

public class Sudoku {
    private static final int SIZE = 9;
    private static final int SUBGRID = 3;
    private int[][] board;
    
    public Sudoku() {
        board = new int[SIZE][SIZE];
        generateSudoku();
    }
    
    private void generateSudoku() {
        fillDiagonal();
        fillRemaining(0, SUBGRID);
        removeDigits(40);
    }
    
    private void fillDiagonal() {
        for (int i = 0; i < SIZE; i += SUBGRID)
            fillBox(i, i);
    }
    
    private void fillBox(int row, int col) {
        Random rand = new Random();
        boolean[] used = new boolean[SIZE + 1];
        for (int i = 0; i < SUBGRID; i++) {
            for (int j = 0; j < SUBGRID; j++) {
                int num;
                do {
                    num = rand.nextInt(SIZE) + 1;
                } while (used[num]);
                used[num] = true;
                board[row + i][col + j] = num;
            }
        }
    }
    
    private boolean fillRemaining(int i, int j) {
        if (j >= SIZE && i < SIZE - 1) {
            i++; j = 0;
        }
        if (i >= SIZE && j >= SIZE)
            return true;
        if (i < SUBGRID) {
            if (j < SUBGRID) j = SUBGRID;
        } else if (i < SIZE - SUBGRID) {
            if (j == (i / SUBGRID) * SUBGRID) j += SUBGRID;
        } else {
            if (j == SIZE - SUBGRID) {
                i++; j = 0;
                if (i >= SIZE) return true;
            }
        }
        
        for (int num = 1; num <= SIZE; num++) {
            if (isSafe(i, j, num)) {
                board[i][j] = num;
                if (fillRemaining(i, j + 1)) return true;
                board[i][j] = 0;
            }
        }
        return false;
    }
    
    private boolean isSafe(int row, int col, int num) {
        return !usedInRow(row, num) && !usedInCol(col, num) && !usedInBox(row - row % SUBGRID, col - col % SUBGRID, num);
    }
    
    private boolean usedInRow(int row, int num) {
        for (int col = 0; col < SIZE; col++)
            if (board[row][col] == num) return true;
        return false;
    }
    
    private boolean usedInCol(int col, int num) {
        for (int row = 0; row < SIZE; row++)
            if (board[row][col] == num) return true;
        return false;
    }
    
    private boolean usedInBox(int startRow, int startCol, int num) {
        for (int i = 0; i < SUBGRID; i++)
            for (int j = 0; j < SUBGRID; j++)
                if (board[i + startRow][j + startCol] == num) return true;
        return false;
    }
    
    private void removeDigits(int count) {
        Random rand = new Random();
        while (count > 0) {
            int row = rand.nextInt(SIZE);
            int col = rand.nextInt(SIZE);
            if (board[row][col] != 0) {
                board[row][col] = 0;
                count--;
            }
        }
    }
    
    public void printBoard() {
        for (int i = 0; i < SIZE; i++) {
            if (i % SUBGRID == 0) System.out.println("-------------------------");
            for (int j = 0; j < SIZE; j++) {
                if (j % SUBGRID == 0) System.out.print("| ");
                System.out.print(board[i][j] == 0 ? " . " : board[i][j] + " ");
            }
            System.out.println("|");
        }
        System.out.println("-------------------------");
    }
    
    public boolean solve() {
        return solveSudoku(0, 0);
    }
    
    private boolean solveSudoku(int row, int col) {
        if (row == SIZE - 1 && col == SIZE)
            return true;
        if (col == SIZE) {
            row++;
            col = 0;
        }
        if (board[row][col] != 0)
            return solveSudoku(row, col + 1);
        
        for (int num = 1; num <= SIZE; num++) {
            if (isSafe(row, col, num)) {
                board[row][col] = num;
                if (solveSudoku(row, col + 1)) return true;
                board[row][col] = 0;
            }
        }
        return false;
    }
    
    public static void main(String[] args) {
        Sudoku sudoku = new Sudoku();
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("Generated Sudoku:");
        sudoku.printBoard();
        
        System.out.println("Do you want to solve it? (yes/no): ");
        String response = scanner.next();
        if (response.equalsIgnoreCase("yes")) {
            if (sudoku.solve()) {
                System.out.println("Solved Sudoku:");
                sudoku.printBoard();
            } else {
                System.out.println("No solution exists.");
            }
        }
        scanner.close();
    }
}
# sudoku-game
