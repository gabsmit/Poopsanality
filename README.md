# Easy_Project_Template

This simple project template is designed to simplify the setup process for new programmers creating static websites.

Just fork the repository and start building! 😊


from dataclasses import dataclass
from drafter import *

@dataclass
class Poop:
    name:str
    age:str
    sex:bool
    color:str
    texture:str
    isBloody:bool
    poopsPerWeek:int
    emptybowel: bool
    poopies:int


@route
def index(poop:Poop)->Page:
    """
    opening page of the poop app
    welcomes user and takes their name
    user chooses which page to head to next
    """
    return Page(poop, [
        "Welcome to Poopsanality!",
        TextBox("name", "User"),
        Button("Take Poo Quiz?", demographic),
        Button("Learn General Info", genInfo),
        #Button("Play Poopie Clicker!", index2),
    ])
                


@route
def genInfo(poop:Poop, name:str)->Page:
    """
    just has gen info about pooping
    """
    poop.name = name
    return Page(poop, [
        "Here are some top tips to improve your overall pooping health!" + "\n",

        "1.) Try to eat 25-35g of fiber everyday, that comes from fruits, veggies, whole grains, and oats." +"\n",
        "2.) Drink at minimum 8-10 cups of water a day to discourage constipation and keep your stool soft!" +"\n",
        "3.) If you have to poop, go poop! Ignoring that vital need of your body can lead to constipation or hemorrhoids." + "\n",
        "4.) If you are having trouble dropping the kids off at the pool, take on a squatting position which will straighten your rectum and ease your poo." +"\n",
        "5.) Exercise encourages intestinal contractions which helps stop constipation." + "\n",
        "6.) Avoid straining while pooping as it can hemorrhoids or pelvic floor damage." +"\n",

        Button("Back to main page", index),
        ])

@route
def demographic(poop:Poop, name:str)-> Page:
    """
    Page thats asks the user their personal info
    """
    poop.name=name
    return Page(poop, [
        "Please enter your age, " + poop.name,
        TextBox("age"),
        "Please select your sex,",
        SelectBox("sex",["Female", "Male", "Other"]),

        Button("Next", quiz),
    ])


  
  
@route
def quiz(poop:Poop, age:str, sex:str)->Page:
    """
    asks user about their pooping habits
    """
    poop.age = age
    if(sex == "Female"):
        poop.sex = True
    else:
        Poop.sex = False

    return Page(poop, [
        "What is your poop's texture?",
        SelectBox("texture", ["Small hard pellet-like chunks", "Lumpy sausage shaped logs", "Cracked, sausage shaped logs", "Smooth, soft, sausage shaped", "Very soft snake-like turds", "Very soft separated blobs","Small bits and pieces with ragged edges", "Watery with no chunks"],),
        
        "What is your poop's color?",
        SelectBox("color", ["Brown", "Black/Dark Brown", "Bright Red", "Green", "White", "Yellow"]),
        
        "How much on average would you say you poop per week?",
        TextBox("poopsPerWeek", "4"),
        
        "Have you noticed any blood in your poop at all this week?",
        SelectBox("bloody", ["Yes", "No"]),
        
        Button("Next", bloodyPoop),
        ])


@route
def bloodyPoop(poop:Poop, texture:str, color:str, poopsPerWeek:str, bloody:str)->Page:
    """
    assigns dataclass info from quiz page to poop instance
    also checks to see if poop is bloody
    if it is it goes to a page detailing the dangers of that
    if not it just goes to the results page
    """
    poop.texture = texture
    poop.color = color
    poop.poopsPerWeek = int(poopsPerWeek)
    
    if(bloody == "Yes"):
        poop.isBloody = True
    elif(bloody == "No"):
        poop.isBloody = False
    
    if(poop.isBloody):
        return bloodyPage(poop)
    else:
        return results(poop)



@route
def bloodyPage(poop:Poop):
    return Page(poop, [
        "BLOODY POOP IS BAD GO TO THE DOCTOR !!!!!!!!!!!!!",
        Button("Main Page", index),
        ])
                
@route
def results(poop:Poop)-> Page:
    sex= "Male"
    if poop.sex:
        sex= "Female"
    realTextureT = determineTextureText(poop)
    realColorT = determineColorText(poop)
    
    return Page(poop, [
        "Results:",
        
        "Name: " + poop.name,
        "Age: " + poop.age,
        "Sex: " + sex,
        "Texture: " + realTextureT,
        "Color: " + realColorT,
        
        Button("Back to Main Page", index),
        
        ])


def determineColorText(poop:Poop)->str:
    
    colors = ["Bright Red", "Brown", "Green", "Yellow", "White", "Black/Dark Brown"]
    
    texts = ["Red is uncommon in all demographics. In some cases, red stool may\n be caused by consumption of red dyes and foods with red pigment.\n In more alarming cases, red stool can be caused by bleeding in\n the intestinal tract, or hemorrhoids; consider seeking medical attention.\n If you’re concerned about having a medical condition, do your\n research on symptoms & seek medical attention as necessary.\n",
             "Brown is the ideal, healthy stool color in all age demographics.\n Phew!\n",
             "Green colored stools are often considered healthy and normal in\n all demographics; typically caused by green food dye, excessive\n consumption of leafy greens.\n However, in certain circumstances green poop may be a result of\n poop passing through the digestive section too quickly, in which\n case, may indicate illness. Bare in mind; this is rarely the\n cause of green poops.\n If you’re concerned about having a medical condition, do your\n research on symptoms & seek medical attention as necessary.\n",
             "Yellow stool color can be common in children 6 months - 5 years\n old, but fairly uncommon in other demographics. Its color is a\n result of excess fat in the stool due to malabsorption of\n nutrients in the digestive process; caused by excess consumption\n of fat, or conditions with traits of malabsorption such as celiac\n disease, liver, and gallbladder issues.\n If you’re concerned about having a medical condition, do your\n research on symptoms & seek medical attention as necessary.\n",
             "White /Light colored stool color is rare in all age demographics.\n In some cases it may be a side effect of medication, otherwise,\n consider seeking medical attention. Its color indicates a lack of\n bile in the stool, which could indicate a bile duct blockage.\n Bile duct blockage is caused by gallstones, infections, and\n inflammation. If you’re concerned about having a medical condition, do your\n research on symptoms & seek medical attention as necessary.\n",
             "Black / Dark Brown stools are uncommon in all age demographics.\n In some cases, it can be caused by medication side effects, or\n consumption of dark pigmented foods.\n In other cases, Its color derives from bleeding in the intestinal\n tract; consider seeking medical attention.\n If you’re concerned about having a medical condition, do your\n research on symptoms & seek medical attention as necessary."] 

    counter = -1
    answer = ""
    for color in colors:
        counter +=1
        if(color == poop.color):
            answer = texts[counter]
    return answer

            
def determineTextureText(poop:Poop)->str:
    texts = ["Small hard pellet-like chunks \nSmall chunks of poop are associated with constipation. Turds like this are able to form their rocky shape from their lack of moisture, indicating dehydration and a lack of fiber in the diet. \nStool conditions may be side effects of medications. \nIf you’re concerned about having a medical condition, do your research on symptoms & seek medical attention as necessary.\nMeanings:\nAssociated with constipation.\nLack of moisture due to dehydration.\nLow fiber intake.\nPossible side effects of medications.\nSolutions:\nDrink more water to stay hydrated.\nIncrease fiber intake (fruits, vegetables, whole grains).\nExercise regularly to stimulate digestion.\nConsult a doctor if constipation persists or is severe.",
             "Sausage shaped logs composed of small hard lumpy chunks \nGenerally, rock-like chunks of poop are associated with constipation. The small chunks forming the logs are able to hold their shape with their lack of moisture, indicating dehydration and a lack of fiber in the diet. \nStool conditions may be side effects of medications. \nIf you’re concerned about having a medical condition, do your research on symptoms & seek medical attention as necessary. \nMeanings:\nMild constipation.\nLack of moisture due to dehydration.\nLow fiber intake.\nPossible side effects of medications.\nSolutions:\nIncrease water intake.\nAdd more fiber to your diet.\nExercise regularly.\nConsult a doctor if constipation persists.",
             "Sausage shaped logs with cracks on the surface \nSmooth, sausage shaped logs (whether cracked or not) are the standard, ideal poop shape. So long as the stool color is healthy, your digestive tract is likely where it needs to be. Keep it up!",
             "Smooth, soft, sausage shaped stool \nSmooth, sausage shaped logs are the standard, ideal poop shape. So long as the stool color is healthy, your digestive tract is likely where it needs to be. Keep it up!",
             "Very soft snake-like turds \nDepending on the frequency, smooth snake-like turds can be considered healthy. Doctors consider it normal to poop smooth turds every 1-3 days; so long as they’re not the only shape you poop. \nIn the context of unhealthy snake-like poops, their meaning  Depends on the frequency of your bowel movements. \nPersistent, overly-frequent soft poops trend towards diarrhea; often associated with viral infections/ stomach bugs, medication side effects, or medical conditions. \nSkinny snake-like logs combined with constipation (infrequent pooping) indicate blockage within the system; Their slim shape is a result of squeezing  through said blockage. Causes of blockage could be medication side effects and medical conditions.\nIf you’re concerned about having a medical condition, do your research on symptoms & seek medical attention as necessary.\nIf you’re concerned about having a medical condition, do your research on symptoms & seek medical attention as necessary.\nMeanings:\nNormal if infrequent (1-3 days).\nIf frequent, could indicate diarrhea or blockage.\nPossible causes: viral infections, stomach bugs, medication \nside effects, or medical conditions.\nSolutions:\nIncrease fiber intake to add bulk.\nStay hydrated.\nMonitor frequency.\nConsult a doctor if persistent or accompanied by other symptoms.",
             "Very soft separated blobs \nVery soft, separated blobs indicate a lack of fiber in the diet. \nMeanings:\nLack of fiber in the diet.\nPossible mild digestive upset.\nSolutions:\nIncrease fiber intake.\nStay hydrated.\nMonitor diet for potential irritants.",
             "Small bits and pieces with ragged edges \nSmall bits and pieces with ragged edges are often associated with \nillness. These turds may be caused by conditions such as GI \nillnesses, stomach bugs, viral infections, or food poisoning. \nIf you’re concerned about having a medical condition, Do your \nresearch on symptoms & seek medical attention as necessary. \nMeanings:\nOften associated with illness (GI issues, infections, food poisoning).\nPossible rapid digestion or irritation in the digestive tract.\nSolutions:\nRest and stay hydrated.\nAvoid fatty or spicy foods.\nConsult a doctor if symptoms persist or worsen.",
             "Watery with no chunks \nLiquid poop is often associated with illness. These turds maybe \ncaused by conditions such as GI illnesses, stomach bugs, viral\ninfections, or food poisoning. \nIf you’re concerned about having a medical condition, do your \nresearch on symptoms & seek medical attention as necessary.\nMeanings:\nDiarrhea, often caused by illness (GI issues, infections, food poisoning).\nPossible rapid digestion or irritation in the digestive tract.\nSolutions:\nRest and stay hydrated.\nAvoid fatty or spicy foods.\n Consult a doctor if diarrhea persists for more than 2-3 days."]
    
    textures = ["Small hard pellet-like chunks", "Lumpy sausage shaped logs", "Cracked, sausage shaped logs", "Smooth, soft, sausage shaped", "Very soft snake-like turds", "Very soft separated blobs","Small bits and pieces with ragged edges", "Watery with no chunks"]
    
    answer = ""
    counter = -1
    for texture in textures:
        counter += 1
        if(texture == poop.texture):
            answer = texts[counter]
            
    return answer
            
        


start_server(Poop("","",False,"","",False,0,False, 0))

