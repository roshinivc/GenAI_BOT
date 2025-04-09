🥗 Building NutritionBOT: Your Desi Meal Planning AI Buddy
NutritionBOT is your friendly Indian nutrition expert powered by GenAI — here to help folks (especially Indian families in the U.S.) eat healthier, smarter, and tastier! Think of it like chatting with your desi dietitian who knows everything from millets to meal prep.

🚀 What Can It Do?
🍛 Suggest Indian-style meal plans (Vegan, Keto, Diabetic, etc.)
🌶️ Customize meals by spice level, allergies, and lifestyle
🛒 Recommend U.S. grocery swaps for Indian ingredients
🧠 Answer your nutrition questions in a friendly convo

Built with LangGraph, LangChain, Mesop, and powered by Google GenAI, this bot tracks your chat history, remembers your preferences, and gives structured responses like recipes with calories, portion sizes, and diet tips.

🧱 Quick Setup & Code Snippets
1. 📦 Clean up & Install Required Libraries

!pip uninstall -qqy kfp jupyterlab spacy fastai google-generativeai
!pip install -qU langgraph==0.3.21 langchain-google-genai==2.1.2 langgraph-prebuilt==0.1.7
!pip install -qU google-genai==1.9.0 pinecone-client pillow graphviz requests langchain-core
2. 🔐 Set Up Your API Key


GOOGLE_API_KEY = UserSecretsClient().get_secret("GOOGLE_API_KEY")
os.environ["GOOGLE_API_KEY"] = GOOGLE_API_KEY

3. 🧠 Define the Conversation State

class NutriQuery(TypedDict):
    messages: Annotated[list, add_messages]
    order: list[str]
    finished: bool
    
4. ✳️ Grounding Logic (Independent Function)

def ground_user_preferences(user_input, user_profile):
    """
    Ground the conversation in user's dietary needs and cultural context.

    Args:
        user_input (str): The user’s latest message.
        user_profile (dict): Stored user preferences (e.g., diet, allergies, spice level).

    Returns:
        dict: A dictionary combining the input and relevant user context.
    """
    return {
        "query": user_input,
        "context": {
            "diet": user_profile.get("diet", "balanced Indian vegetarian"),
            "allergies": user_profile.get("allergies", []),
            "spice_level": user_profile.get("spice_level", "medium"),
            "region": user_profile.get("region", "South Indian"),
            "location": user_profile.get("location", "USA")
        }
    }
5. 🗣️ Set System Instructions

NUTRIABOT_SYSINT = (
    "system",
    "You're DesiNutritionBot — help Indian families in the U.S. eat better, stay healthy, and enjoy their culture!"
)
6. 🔁 Add Auto-Retry for API Limits


is_retriable = lambda e: (isinstance(e, genai.errors.APIError) and e.code in {429, 503})
genai.models.Models.generate_content = retry.Retry(predicate=is_retriable)(genai.models.Models.generate_content)

7. ✅ Use Grounding in Your Main Loop

user_input = "I need a quick lunch idea with less oil"
user_profile = {
    "diet": "diabetic-friendly",
    "allergies": ["peanuts"],
    "spice_level": "low",
    "region": "Telugu",
    "location": "California"
}

grounded_request = ground_user_preferences(user_input, user_profile)
You can now pass grounded_request["query"] and grounded_request["context"] to your LLM function!

✨ That’s It!
Now your NutritionBOT is not just smart, it’s grounded in real user preferences — delivering responses that are personal, culturally accurate, and super useful.

NutritionBOT is more than just a chatbot—it’s a bridge between tradition and tech. Whether you're building an app for South Asian expats or experimenting with personalized nutrition, this is a great way to mix GenAI + Python + culture into something delightful and useful.

💬 Try it out with prompts like:
"Give me a diabetic-friendly Indian meal plan for weekdays."
"What’s a good substitute for curry leaves in the U.S.?"
"Suggest high-protein vegetarian Indian snacks."





Note: Used AI app to generate readmefile from code

