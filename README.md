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

-----------------------------------------
✅ Final App Structure
-----------------------------------------
1. activity_main.xml
------------------------------------------
{
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layoutRoot"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/startEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Start"
        android:inputType="number" />

    <EditText
        android:id="@+id/endEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="End"
        android:inputType="number" />

    <Button
        android:id="@+id/submitButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Submit" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="16dp"/>
</LinearLayout>
}
-----------------------------------
2. item_timer.xml
-----------------------------------
{
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:orientation="vertical">

    <TextView
        android:id="@+id/itemText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:textColor="#000000"
        android:text="Timer Item" />
</LinearLayout>
}
---------------------------------
3. TimerItem.kt
---------------------------------
{
data class TimerItem(
    val number: Int,
    var isDone: Boolean = false
)
}
--------------------
4. TimerAdapter.kt
--------------------
{
import android.graphics.Color
import android.os.CountDownTimer
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class TimerAdapter(private val items: List<TimerItem>) :
    RecyclerView.Adapter<TimerAdapter.TimerViewHolder>() {

    inner class TimerViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val itemText: TextView = itemView.findViewById(R.id.itemText)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TimerViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_timer, parent, false)
        return TimerViewHolder(view)
    }

    override fun onBindViewHolder(holder: TimerViewHolder, position: Int) {
        val item = items[position]

        if (item.isDone) {
            holder.itemText.text = "Done"
            holder.itemText.setTextColor(Color.GREEN)
        } else {
            holder.itemText.text = "Counting ${item.number}..."
            holder.itemText.setTextColor(Color.BLACK)

            object : CountDownTimer(2000, 1000) {
                override fun onTick(millisUntilFinished: Long) {}

                override fun onFinish() {
                    item.isDone = true
                    notifyItemChanged(position)
                }
            }.start()
        }
    }

    override fun getItemCount(): Int = items.size
}

}
---------------------------------
5. `````MainActivity.kt`````
---------------------------------
{        
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

class MainActivity : AppCompatActivity() {

    private lateinit var startEditText: EditText
    private lateinit var endEditText: EditText
    private lateinit var submitButton: Button
    private lateinit var recyclerView: RecyclerView
    private lateinit var adapter: TimerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        startEditText = findViewById(R.id.startEditText)
        endEditText = findViewById(R.id.endEditText)
        submitButton = findViewById(R.id.submitButton)
        recyclerView = findViewById(R.id.recyclerView)

        recyclerView.layoutManager = LinearLayoutManager(this)

        submitButton.setOnClickListener {
            val start = startEditText.text.toString().toIntOrNull()
            val end = endEditText.text.toString().toIntOrNull()

            if (start != null && end != null && end >= start) {
                val items = (start..end).map { TimerItem(it) }
                adapter = TimerAdapter(items)
                recyclerView.adapter = adapter
            } else {
                Toast.makeText(this, "Enter valid start and end values", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
}

✅2) FINAL WORKING CODE

class GameActivity : AppCompatActivity(), BoxAdapter.OnItemClickListener {

    private lateinit var recyclerView: RecyclerView
    private lateinit var adapter: BoxAdapter
    private var boxStates = mutableListOf<Int>() // 0=white, 1=blue, 2=red, 3=gray

    private var lastRedIndex: Int? = null
    private var totalBoxes = 0
    private var redCount = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_game)

        totalBoxes = intent.getIntExtra("BOX_COUNT", 0)
        boxStates = MutableList(totalBoxes) {
            0
        } // all white

        recyclerView = findViewById(R.id.recyclerViewBoxes)
        recyclerView.layoutManager = GridLayoutManager(this, 4)

        adapter = BoxAdapter(boxStates,this)
        recyclerView.adapter = adapter

        setRandomBlue()
    }

    private fun onBlueBoxClicked(clickedIndex: Int) {
        // Set clicked blue box → red
        boxStates[clickedIndex] = 2
        redCount++

        // Set previous red → gray
        lastRedIndex?.let {
            boxStates[it] = 3
        }

        lastRedIndex = clickedIndex

        // Set new random white box → blue (if any left)
        if (redCount < totalBoxes) {
            setRandomBlue()
        }

        adapter.notifyDataSetChanged()
    }

    private fun setRandomBlue() {
        val whiteIndices = boxStates.withIndex()
            .filter { it.value == 0 }
            .map { it.index }

        if (whiteIndices.isNotEmpty()) {
            val randomIndex = whiteIndices[Random.nextInt(whiteIndices.size)]
            boxStates[randomIndex] = 1
        }
    }

    override fun onItemClick(position: Int) {
        onBlueBoxClicked(position)
    }
}
---------------------------------------
class BoxAdapter(
    private val boxStates: List<Int>,
    private val onItemClickListener: OnItemClickListener
) : RecyclerView.Adapter<BoxAdapter.BoxViewHolder>() {

    inner class BoxViewHolder(val view: View) : RecyclerView.ViewHolder(view) {
        init {
            view.setOnClickListener {
                if (boxStates[adapterPosition] == 1) {
                    onItemClickListener.onItemClick(adapterPosition)
                }
            }
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BoxViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_box, parent, false)
        return BoxViewHolder(view)
    }

    override fun onBindViewHolder(holder: BoxViewHolder, position: Int) {
        val color = when (boxStates[position]) {
            0 -> Color.WHITE
            1 -> Color.BLUE
            2 -> Color.RED
            3 -> Color.GRAY
            else -> Color.WHITE
        }
        holder.view.setBackgroundColor(color)
    }

    interface OnItemClickListener {
        fun onItemClick(position: Int)
    }

    override fun getItemCount(): Int = boxStates.size
}
---------------------
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:layout_margin="6dp"
    android:background="@android:color/white"/>
/////////////////////////////////////////
for timer 
////////////////////////////

class MainActivity : AppCompatActivity() {

    private lateinit var startEditText: EditText
    private lateinit var endEditText: EditText
    private lateinit var submitButton: Button
    private lateinit var recyclerView: RecyclerView
    private lateinit var adapter: TimerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        startEditText = findViewById(R.id.startEditText)
        endEditText = findViewById(R.id.endEditText)
        submitButton = findViewById(R.id.submitButton)
        recyclerView = findViewById(R.id.recyclerView)

        recyclerView.layoutManager = LinearLayoutManager(this)

        submitButton.setOnClickListener {
            val start = startEditText.text.toString().toIntOrNull()
            val end = endEditText.text.toString().toIntOrNull()

            if (start != null && end != null && end >= start) {
                val items = (start..end).map { TimerItem(it) }
                adapter = TimerAdapter(items)
                recyclerView.adapter = adapter
            } else {
                Toast.makeText(this, "Enter valid start and end values", Toast.LENGTH_SHORT).show()
            }
        }
    }
}
////////////
adapter
///////////////

class TimerAdapter(private val items: List<TimerItem>) :
    RecyclerView.Adapter<TimerAdapter.TimerViewHolder>() {

    inner class TimerViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val itemText: TextView = itemView.findViewById(R.id.itemText)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TimerViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_timer, parent, false)
        return TimerViewHolder(view)
    }

    override fun onBindViewHolder(holder: TimerViewHolder, @SuppressLint("RecyclerView") position: Int) {
        val item = items[position]

        if (item.isDone) {
            holder.itemText.text = "Done"
            holder.itemText.setTextColor(Color.GREEN)
        } else {
            holder.itemText.text = "Counting ${item.number}..."
            holder.itemText.setTextColor(Color.BLACK)

            object : CountDownTimer(2000, 1000) {
                override fun onTick(millisUntilFinished: Long) {}

                override fun onFinish() {
                    item.isDone = true
                    notifyItemChanged(position)
                }
            }.start()
        }
    }


    override fun getItemCount(): Int = items.size
}
////
data class
/////////
data class TimerItem(
    val number: Int,
    var isDone: Boolean = false
)




