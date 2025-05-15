# Clock Project

This project is a simple digital clock built using HTML and CSS. The clock displays the current time in a digital format and is designed to be responsive and visually appealing.

## Features

- **Real-time Display**: Shows the current hours, minutes, and seconds.
- **Responsive Design**: Adjusts to different screen sizes for optimal viewing.
- **Minimalist Style**: Clean and simple design for easy readability.

## Files Included

- `index.html`: Contains the HTML structure of the clock.
- `style.css`: Includes the CSS styles for the clock design.

## Usage

1. Clone the repository to your local machine.
   ```bash
   git clone https://github.com/theankitparmar/Clock.git



Great — here’s a clean and professional implementation plan for your Android app with two activities:


---

🔧 App Features Overview

1. Activity 1 (InputActivity):

Input a number between 4 to 10.

Button to proceed to the second activity.



2. Activity 2 (GridActivity):

Dynamically show a NxN grid (e.g., 4x4...10x10).

On clicking a cell:

Blink the selected row, column, and both diagonals (if applicable).






---

🧱 Tech Stack

Language: Kotlin

Architecture: MVVM not needed for this simple logic

Views: ConstraintLayout, GridLayout, Dynamic UI

Animations: Alpha blinking via ObjectAnimator or ValueAnimator



---

✅ Step-by-Step Implementation


---

1. activity_input.xml

<!-- Simple layout for input -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:gravity="center"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="24dp">

    <EditText
        android:id="@+id/numberInput"
        android:hint="Enter number (4-10)"
        android:inputType="number"
        android:maxLength="2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/submitButton"
        android:text="Generate Grid"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp" />
</LinearLayout>


---

2. InputActivity.kt

class InputActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_input)

        val input = findViewById<EditText>(R.id.numberInput)
        val button = findViewById<Button>(R.id.submitButton)

        button.setOnClickListener {
            val number = input.text.toString().toIntOrNull()

            if (number != null && number in 4..10) {
                val intent = Intent(this, GridActivity::class.java)
                intent.putExtra("GRID_SIZE", number)
                startActivity(intent)
            } else {
                Toast.makeText(this, "Enter a number between 4 and 10", Toast.LENGTH_SHORT).show()
            }
        }
    }
}


---

3. activity_grid.xml

<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <GridLayout
        android:id="@+id/gridLayout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:alignmentMode="alignMargins"
        android:columnCount="4"
        android:rowCount="4"
        android:padding="16dp"
        android:layout_gravity="center"
        android:useDefaultMargins="true" />
</ScrollView>


---

4. GridActivity.kt

class GridActivity : AppCompatActivity() {

    private lateinit var gridLayout: GridLayout
    private var gridSize = 4
    private val cellViews = mutableListOf<TextView>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_grid)

        gridLayout = findViewById(R.id.gridLayout)
        gridSize = intent.getIntExtra("GRID_SIZE", 4)

        gridLayout.columnCount = gridSize
        gridLayout.rowCount = gridSize

        generateGrid()
    }

    private fun generateGrid() {
        for (row in 0 until gridSize) {
            for (col in 0 until gridSize) {
                val cell = TextView(this).apply {
                    text = "${row * gridSize + col + 1}"
                    textSize = 18f
                    gravity = Gravity.CENTER
                    setBackgroundColor(Color.LTGRAY)
                    setTextColor(Color.BLACK)
                    setPadding(16, 16, 16, 16)
                    layoutParams = ViewGroup.LayoutParams(160, 160)

                    setOnClickListener {
                        blinkLines(row, col)
                    }
                }

                gridLayout.addView(cell)
                cellViews.add(cell)
            }
        }
    }

    private fun blinkLines(row: Int, col: Int) {
        for (r in 0 until gridSize) {
            blinkCell(getCellAt(r, col)) // Column
            blinkCell(getCellAt(row, r)) // Row
        }

        // Diagonal (top-left to bottom-right)
        if (row == col) {
            for (i in 0 until gridSize) {
                blinkCell(getCellAt(i, i))
            }
        }

        // Anti-diagonal (top-right to bottom-left)
        if (row + col == gridSize - 1) {
            for (i in 0 until gridSize) {
                blinkCell(getCellAt(i, gridSize - 1 - i))
            }
        }
    }

    private fun getCellAt(row: Int, col: Int): TextView {
        return cellViews[row * gridSize + col]
    }

    private fun blinkCell(view: View) {
        val animator = ObjectAnimator.ofFloat(view, "alpha", 1f, 0.3f, 1f)
        animator.duration = 400
        animator.repeatCount = 2
        animator.start()
    }
}


---

✅ Final Touches:

Add necessary permissions in AndroidManifest.xml

Apply better UI via Material Design if needed

Add bounds checking to avoid crashes

Handle orientation change (optional)



---

Would you like me to:

Create a full GitHub project structure?

Convert this to Jetpack Compose version?

Add grid reset, random color blink, or sound effect?


Let me know how you’d like to evolve this idea.




