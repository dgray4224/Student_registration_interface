#+###############################################################+
#|                                                               |
#|                      Course: CISP 71                          |
#|                      Term: Spring 2021                        |              
#|             Institution: Mount San Antonio College            |                           
#|                 Instructor: Sohair Zaki                       |                                                                             
#|                                                               |
#+################################################################+
#|                                                               |
#|                 Program: CSULB Course Listings                | 
#|                   Author: Daniel Garcia                       |
#|             Contact: dgarcia249@student.mtsac.edu             |
#|                  Version: 3.9.2                               |
#|                                                               |
#+###############################################################+
                                                               
#+###############################################################+
#|                                                               |
#|                      Course: CISP 71                          |
#|                      Term: Spring 2021                        |              
#|             Institution: Mount San Antonio College            |                           
#|                 Instructor: Sohair Zaki                       |                                                                             
#|                                                               |
#+################################################################+
#|                                                               |
#|                 Program: CSULB Course Listings                | 
#|                   Author: Daniel Garcia                       |
#|             Contact: dgarcia249@student.mtsac.edu             |
#|                  Version: 3.9.2                               |
#|                                                               |
#+###############################################################+
                                                               
#+###############################################################+
#|             Required Libraries and Imports                   |#
#+###############################################################+
from os import read
from tkinter import *                      # allows to build the core components for graphical user interface
from tkinter import ttk                    # allows to build the treeview for displaying data
import tkinter.messagebox as mb            # allows us to communicate mesasges/alerts to user 
from PIL import ImageTk, Image             # allows to import png files as images  
import sqlite3                             # connects program to database in sqlite 
       
#+###############################################################+

########################## Basic Window setup ########################+
# create window
root = Tk()

# create title
root.title('Course List (CSULB)')

# size of the window
root.geometry('875x450')

#create label widget for title
title = Label(root, text = 'CSULB COURSES', font = ('Arial Black', 18), fg = 'gold', )

########################### Images ###################################+

# add path and change icon on window
path = 'C:/Users/dgray/OneDrive/Desktop/Prototypes/'
root.iconbitmap('C:/Users/dgray/OneDrive/Pictures/Icons/eyescan2.ico')

# add path for image file
my_img = ImageTk.PhotoImage(Image.open('C:/Users/dgray/OneDrive/Pictures/Icons/Math1.png'))
my_label = Label(image = my_img, width = 325, height = 150)

# place image
my_label.place(x = 5, y = 40)

######################## Labels/Entry boxes/Drop down list ######################+

# create label widgets
crn = Label(root, text = "CRN:")
crsetitle = Label(root, text = "Course/Course Title:")
units = Label(root, text = "Amount of Units:")
dates = Label(root, text = "Start date-End date:")
requirement = Label(root, text = "Requirement or Elective?")
prerequisite = Label(root, text = "Prerequisites:")

# create entry box
crn_entry = Entry(root,width= 35)
crsetitle_entry = Entry(root,width= 35)
dates_entry = Entry(root, width= 35)
requirement_entry = Entry(root, width = 35)
prerequisite_entry = Entry(root,width= 35)

# create drop down menu 
# define clicked variable
clicked = IntVar()
my_list = [0, 1, 2, 3, 4, 5]

units_drop = OptionMenu(root, clicked, *my_list) 

# set default values for entry box to specify desired format
crsetitle_entry.insert(1,'Format ex: Math1/Algebra')
dates_entry.insert(1,'Format ex: "9/01-12/15"')
prerequisite_entry.insert(1, 'Format ex: "CISP 71"')

# place labels on window 
title.place(x = 300, y = 1)
crn.place(x = 350, y = 50)
crsetitle.place(x = 350, y = 75)
units.place(x = 350, y = 110)
dates.place(x = 350, y = 140)
requirement.place(x = 350, y = 165)
prerequisite.place(x = 350, y = 190)

# place entry box on window
crn_entry.place(x = 500, y = 50)
crsetitle_entry.place(x = 500, y = 75)
dates_entry.place(x = 500, y = 140)
requirement_entry.place(x = 500, y = 165)
prerequisite_entry.place(x = 500, y = 190)

# place drop down menu on window
units_drop.place(x = 525, y = 100)

#+############################## Class Database ###########################################+

path = r'C:\Users\dgray\OneDrive\Python\Projects'
conn=sqlite3.connect(path+"courses.db")
class Database: 
    def __init__(self, db):
        self.conn = sqlite3.connect(db)
        self.cur = self.conn.cursor()
        self.cur.execute('''CREATE TABLE IF NOT EXISTS csulb_student 
                         (CRN INTEGER PRIMARY KEY,
                         Title text,
                         Units integer,
                         Dates text,
                         Requirement text,
                         Prerequisites text)''')
        self.conn.commit()  

    def fetch(self):
        self.cur.execute("SELECT * FROM csulb_student")
        rows = self.cur.fetchall()
        return rows

    def insert(self, CRN, Title, Units, Dates, Requirement, Prerequisite):
        self.cur.execute("INSERT INTO csulb_student VALUES (?, ?, ?, ?, ?, ?)",
                        (CRN, Title, Units, Dates, Requirement, Prerequisite))
        self.conn.commit()
    
    def remove(self, CRN):
        self.cur.execute('DELETE FROM csulb_student WHERE CRN=?', (CRN))
        self.conn.commit()

    def update(self, CRN, Title, Units, Dates, Requirement, Prerequisite):
        self.cur.execute('''UPDATE csulb_student SET
                        CRN=?,
                        Title=?,
                        Units=?,
                        Dates=?,
                        Requirement=?,
                        Prerequisite=?''',
                        (CRN, Title, Units, Dates, Requirement, Prerequisite))
        self.conn.commit()

    def delete(self, CRN):
        self.conn.close()

db = Database(path+'courses.db')

#+########################### Functions ############################################+

# returns default value on drop down list. Using to implement in Clear() function
def Start():
    clicked.set(my_list[0]) 

# function for "Read" button. It will first clear all entry boxes and drop down menus, then fill the entry boxes with the selected records in the trreeview.
def Read():
    clear()
    s_item = my_tree.focus() 
    my_dict = my_tree.item(s_item)
    dict_list = my_dict["values"]
    crn_entry.insert(0, my_dict['text'])
    crsetitle_entry.insert(0,dict_list[0])
    dates_entry.insert(0,dict_list[2]) 
    requirement_entry.insert(0,dict_list[3])
    clicked.set(dict_list[1])
    prerequisite_entry.insert(0,dict_list[4])

# This function removes selected records in treeview, but not in database. 
# made to help with other functions like Delete() and Update()
def remove():
    x = my_tree.selection()
    for record in x:
        my_tree.delete(x)

# This function permanently deletes the selected record from the treeview and from the database.
# functionality: user selects a record, presses delete, message box appears warniung that the 'yes' will permanently delete record from treeview AND database.  
def Delete():
    msgbox = mb.askquestion('Delete Record', 'Are you sure you want to Delete record? Selecting "Yes" will permanently delete record from display and from database', icon = 'warning')
    if msgbox == 'yes':
        remove()
        conn=sqlite3.connect(path+"courses.db")
        qry="DELETE from csulb_student where crn=?;"
        c=conn.cursor()
        c.execute(qry, (crn_entry.get(),))
        conn.commit()

# function for "update" button. 
# permanently deletes the selected record from treeview and database, then adds the changes. its basically a "delete" button and an "add" button in one. 
# doesnt connect to database properly if changing the CRN. I believe its because qry is defined in terms of CRN. how to change?????
def Update():
    msgbox = mb.askquestion('Update Record', 'Are you sure you want to make changes to the record?', icon = 'warning')
    if msgbox == 'yes':
        remove()
        conn=sqlite3.connect(path+"courses.db")
        qry="DELETE from csulb_student where crn=?;"
        c=conn.cursor()
        c.execute(qry, (crn_entry.get(),))
        conn.commit()
        addRecord()

# function for "Add" button.
# takes the entries from user and inputs them into the treeview and database
def addRecord():
    my_tree.insert('', 1, text = crn_entry.get(), values = (crsetitle_entry.get(), clicked.get(), dates_entry.get(), requirement_entry.get(), prerequisite_entry.get()))
    conn = sqlite3.connect(path+"courses.db")
    c=conn.cursor()
    c.execute("INSERT INTO csulb_student VALUES (?,?,?,?,?,?)",
            db.insert(crn_entry.get(),crsetitle_entry.get(),clicked.get(),dates_entry.get(),requirement_entry.get(),prerequisite_entry.get() ))
    conn.commit()
    clear()
    conn.close()

# function for "retrieve" button
# grabs all the records from the database and inputs them into the treeview
def Retrieve():
    for row in my_tree.get_children():
        my_tree.delete(row)
    conn=sqlite3.connect(path+"courses.db")
    c=conn.cursor()
    c.execute('select * from csulb_student')
    records = c.fetchall()
    for record in records: 
        my_tree.insert('', 1, text = record[0], values = ( record[1], record[2], record[3], record[4], record[5]))
        

#  function for "clear" entry boxes
# deletes all content from entry box and sets drop down menu to its default by calling Start() function. 
def clear():
    crn_entry.delete(0,END) 
    crsetitle_entry.delete(0,END)
    dates_entry.delete(0,END) 
    requirement_entry.delete(0,END)
    Start()
    prerequisite_entry.delete(0,END)

#+############################# Buttons ##################################+  

# create buttons
readbtn = Button(root, text = 'Read', command = Read)
Addbtn = Button(root, text = 'Add', command = addRecord)
updatebtn = Button(root, text = 'Update', command = Update)
deletebtn = Button(root, text = 'Delete', fg = 'white', bg = 'black', command = Delete)
retrievebtn = Button(root, text = 'Retrieve', command = Retrieve)
clearbtn = Button(root, text = 'Clear', command = clear)

removebtn = Button(root, text = 'remove from tree', command = remove)


# place buttons on window
readbtn.place(x = 750, y = 50)
Addbtn.place(x = 750, y = 100)
updatebtn.place(x = 800, y = 50)
deletebtn.place(x = 800, y = 150)
retrievebtn.place(x = 800, y = 100)
clearbtn.place(x=750, y = 150)

removebtn.place(x= 750, y= 10)

############################# Treeview ##########################################+

# create treeview in root
my_tree = ttk.Treeview(root, selectmode = 'browse')

# Define columns
my_tree['columns'] = ('#0', '#1', '#2', '#3', '#4', '#5')

# Format columns
my_tree.column('#0', anchor = CENTER, width = 85,  stretch = False)
my_tree.column('#1', anchor = CENTER, width = 250, minwidth = 50)
my_tree.column('#2', anchor = CENTER, width = 40, stretch = False)
my_tree.column('#3', anchor = CENTER, width = 120, stretch = False)
my_tree.column('#4', anchor = CENTER, width = 135, stretch = False)
my_tree.column('#5', anchor = CENTER, width = 100, stretch = False)

# Create headings
my_tree.heading('#0', text = 'CRN', anchor = CENTER)
my_tree.heading('#1', text = 'Course Title', anchor = CENTER)
my_tree.heading('#2', text = 'Units', anchor = CENTER)
my_tree.heading('#3', text = 'Start/End Date', anchor = CENTER)
my_tree.heading('#4', text = 'Requirement/Elective', anchor = CENTER)
my_tree.heading('#5', text = 'Prerequisites', anchor = CENTER)

# add some style to tree
style = ttk.Style()
style.configure('Treeview',
                background = 'silver',
                fg = 'black',
                rowheight = 50)

# implement theme
# theme_use allows to adopt themes already stored
style.theme_use('alt')

# place treeview on screen 
my_tree.place(x = 25, y = 215, height = 200, width = 810)

############################ Scroll bars ####################################+ 

# create vertical scroll bars for tree view
vertScroll = ttk.Scrollbar(root, orient = 'vertical', command = my_tree.yview)

# place vertical scroll bar for treeview
vertScroll.place(x = 835, y = 215, height = 200)

# configure vertical scroll bar for treeview
my_tree.configure(yscrollcommand = vertScroll.set)

# Create horizontal scroll bar for treeview
horzScroll = ttk.Scrollbar(root, orient = 'horizontal', command = my_tree.xview)

# place horizontal scrollbar in treeview
horzScroll.place(x = 25, y = 400, width = 810)

# configure horizontal scroll bar in tree view
my_tree.configure(xscrollcommand = horzScroll.set)

# bind the tree view to the function show_selected_record
# not sure if this does anything
my_tree.bind("<<TreeviewSelect>>", Read)

# start mainloop of the root
root.mainloop()