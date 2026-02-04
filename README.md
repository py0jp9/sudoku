package sudoku;

import javax.swing.*;
import java.awt.*;
import java.util.Random;

public class Sudoku extends JFrame {

    private JTextField[][] cells = new JTextField[9][9];
    private int[][] solution = new int[9][9];
    private int[][] puzzle = new int[9][9];

    public Sudoku() {
        setTitle("Sudoku");
        setSize(500, 550);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());

        JPanel grid = new JPanel(new GridLayout(9, 9));
        Font font = new Font("Arial", Font.BOLD, 18);

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                JTextField cell = new JTextField();
                cell.setHorizontalAlignment(JTextField.CENTER);
                cell.setFont(font);

                final int row = i;
                final int col = j;

                cell.addKeyListener(new java.awt.event.KeyAdapter() {
                    @Override
                    public void keyReleased(java.awt.event.KeyEvent e) {
                        checkCell(row, col);
                    }
                });

                cells[i][j] = cell;
                grid.add(cell);
            }
        }

        JButton newGame = new JButton("Novo Jogo");
        newGame.addActionListener(e -> startNewGame());

        add(grid, BorderLayout.CENTER);
        add(newGame, BorderLayout.SOUTH);

        startNewGame();
    }

    private void startNewGame() {
        solution = new int[9][9];
        puzzle = new int[9][9];

        generateSolution();
        copySolution();
        removeNumbers(40);
        updateBoard();
    }

    private void updateBoard() {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (puzzle[i][j] != 0) {
                    cells[i][j].setText(String.valueOf(puzzle[i][j]));
                    cells[i][j].setEditable(false);
                    cells[i][j].setBackground(Color.LIGHT_GRAY);
                } else {
                    cells[i][j].setText("");
                    cells[i][j].setEditable(true);
                    cells[i][j].setBackground(Color.WHITE);
                }
                cells[i][j].setForeground(Color.BLACK);
            }
        }
    }

    private void checkCell(int row, int col) {
        if (!cells[row][col].isEditable()) return;

        try {
            int value = Integer.parseInt(cells[row][col].getText());
            if (value == solution[row][col]) {
                cells[row][col].setForeground(Color.BLACK);
            } else {
                cells[row][col].setForeground(Color.RED);
            }
        } catch (Exception e) {
            cells[row][col].setForeground(Color.BLACK);
        }
    }

    private void generateSolution() {
        fillBoard(solution);
    }

    private boolean fillBoard(int[][] board) {
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                if (board[row][col] == 0) {
                    int[] numbers = shuffleNumbers();
                    for (int num : numbers) {
                        if (isValid(board, row, col, num)) {
                            board[row][col] = num;
                            if (fillBoard(board)) return true;
                            board[row][col] = 0;
                        }
                    }
                    return false;
                }
            }
        }
        return true;
    }

    private boolean isValid(int[][] board, int row, int col, int num) {
        for (int i = 0; i < 9; i++) {
            if (board[row][i] == num || board[i][col] == num)
                return false;
        }

        int startRow = (row / 3) * 3;
        int startCol = (col / 3) * 3;

        for (int i = startRow; i < startRow + 3; i++) {
            for (int j = startCol; j < startCol + 3; j++) {
                if (board[i][j] == num)
                    return false;
            }
        }
        return true;
    }

    private int[] shuffleNumbers() {
        int[] nums = {1,2,3,4,5,6,7,8,9};
        Random r = new Random();
        for (int i = 0; i < nums.length; i++) {
            int j = r.nextInt(nums.length);
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
        return nums;
    }

    private void copySolution() {
        for (int i = 0; i < 9; i++)
            for (int j = 0; j < 9; j++)
                puzzle[i][j] = solution[i][j];
    }

    private void removeNumbers(int amount) {
        Random r = new Random();
        while (amount > 0) {
            int row = r.nextInt(9);
            int col = r.nextInt(9);
            if (puzzle[row][col] != 0) {
                puzzle[row][col] = 0;
                amount--;
            }
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new Sudoku().setVisible(true);
        });
    }
}
