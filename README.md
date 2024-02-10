# Importing necessary libraries
from dash import Dash, html, dcc, Input, Output, State
import random

# External stylesheets for the app to enhance the background
external_stylesheets = [
    'https://codepen.io/MarcoGuglielmelli/pen/ExGYae',
    'https://fonts.googleapis.com/css2?family=Bubblegum+Sans&display=swap'
]

# Creating the Dash app instance
app = Dash(__name__, external_stylesheets=external_stylesheets)

# Function to generate random star animation
# The function uses a list comprehension to generate 100 div elements, each representing a star.
# Each star is styled using CSS properties to control its appearance and animation.
# The random.randint() and random.uniform() functions from the random module are used to generate random positions, durations, and delays for each star's animation.
def generate_stars():
    # Generating stars using HTML div elements
    return [html.Div(
        id='star-' + str(i),
        className='star',
        style={
            'position': 'absolute',
            'backgroundColor': 'white',
            'borderRadius': '50%',
            'width': '4px',
            'height': '4px',
            'left': str(random.randint(0, 100)) + '%',
            'top': str(random.randint(0, 100)) + '%',
            'animation': 'twinkle ' + str(random.uniform(1, 3)) + 's linear infinite',
            'animation-delay': str(random.uniform(0, 3)) + 's'
        }
    ) for i in range(100)]

# Defining the layout of the app
app.layout = html.Div(
    style={
        'position': 'relative',
        'backgroundColor': 'black',
        'color': 'white',
        'height': '100vh',
        'fontFamily': 'Bubblegum Sans'
    },
    children=[
        # Container for stars
        html.Div(
            id='stars-container',
            style={'position': 'absolute', 'width': '100%', 'height': '100%'},
            children=generate_stars()
        ),
        # Main content of the app
        html.Div(
            style={'position': 'absolute', 'top': '50%', 'left': '50%', 'transform': 'translate(-50%, -50%)'},
            children=[
                # App title
                html.H1(children='WELCOME TO MOOD BOOSTER', style={'textAlign': 'bottom', 'fontSize': '48px'}),
                # Mood selection section
                html.Div(
                    style={'textAlign': 'center'},
                    children=[
                        html.H3("HOW ARE YOU FEELING TODAY?", style={'marginBottom': 40, 'fontSize': '24px'}),
                        dcc.RadioItems( # allows user to pick one option out of the other
                            id='mood-selector',
                            options=[
                                {'label': '😊 HAPPY', 'value': 'happy'},
                                {'label': '😔 SAD', 'value': 'sad'},
                                {'label': '😡 ANGRY', 'value': 'angry'},
                                {'label': '😴 TIRED', 'value': 'tired'}
                            ],
                            style={'display': 'inline-block'},
                            labelStyle={'marginRight': '10px', 'fontSize': '20px'}
                        ),
                        html.Div(id='output-message', style={'marginTop': 30}),
                        html.Button('GET A MOOD BOOST!', id='mood-boost-button', n_clicks=0, style={'marginTop': 15, 'backgroundColor': '#4B0082', 'color': 'white', 'borderRadius': '5px', 'padding': '10px 20px', 'fontSize': '20px'}),
                    ]
                ),
                html.Div(id='mood-gif', style={'marginTop': 20}),
            ]
        ),
        # Mental Health Resources button
        html.Div(
            id='resource-button-container',
            style={'position': 'absolute', 'top': '20px', 'right': '20px'},
            children=[
                html.Button('MENTAL HEALTH RESOURCES', id='resource-button', n_clicks=0, style={'backgroundColor': '#4B0082', 'color': 'white', 'borderRadius': '5px', 'padding': '10px 20px', 'fontSize': '20px'})
            ]
        ),
        # Modal for displaying mental health resources
        html.Div(
            id='resource-modal',
            style={'position': 'fixed', 'top': '0', 'left': '0', 'width': '100%', 'height': '100%', 'backgroundColor': 'rgba(0,0,0,0.5)', 'display': 'none'},
            children=[
                html.Div(
                    style={'position': 'absolute', 'top': '50%', 'left': '50%', 'transform': 'translate(-50%, -50%)', 'backgroundColor': 'white', 'padding': '20px', 'borderRadius': '10px'},
                    children=[
                        html.H2("MENTAL HEALTH RESOURCES", style={'textAlign': 'center', 'fontSize': '32px'}),
                        # List of mental health resources
                        html.Ul([ #uL means unorder list
                            html.Li(html.A("UIC Counseling", href="https://counseling.uic.edu", target="_blank")), # li means list structures
                            html.Li(html.A("National Alliance on Mental Illness (NAMI)", href="https://www.nami.org/", target="_blank")),
                            html.Li(html.A("National Suicide Prevention Lifeline", href="https://suicidepreventionlifeline.org/", target="_blank")),
                            html.Li(html.A("Crisis Text Line", href="https://www.crisistextline.org/", target="_blank")),# target means that it will open it in new tab
                            html.Li(html.A("MentalHealth.gov", href="https://www.mentalhealth.gov/", target="_blank")),
                            html.Li(html.A("Support Group", href="https://counseling.uic.edu/group-3/", target="_blank")),
                        ]),
                        html.Button("Close", id="close-modal-button", style={'marginTop': '20px', 'backgroundColor': '#FF4500', 'color': 'white', 'borderRadius': '5px', 'padding': '10px 20px', 'fontSize': '20px'})
                    ]
                )
            ]
        )
    ]
)

# Callback to toggle display of the resource modal
@app.callback( # to callback function and will chnage until specified input
    Output('resource-modal', 'style'),
    [Input('resource-button', 'n_clicks'),
     Input('close-modal-button', 'n_clicks')],
    [State('resource-modal', 'style')]
)
def toggle_modal(resource_clicks, close_clicks, modal_style):
    resource_clicks = resource_clicks or 0
    close_clicks = close_clicks or 0

    if resource_clicks > close_clicks:
        modal_style['display'] = 'block'
    else:
        modal_style['display'] = 'none'
    return modal_style

# Callback to display mood-specific message
@app.callback(
    Output('output-message', 'children'),
    [Input('mood-selector', 'value')]
)
def display_message(selected_mood):
    if selected_mood:
        if selected_mood == 'happy':
            return html.Div ("If life gives you lemons, make lemonade... then find someone whose life gave them vodka and have a party!", style={'fontSize': '25px'})
        elif selected_mood == 'sad':
            return html.Div("Why did the tomato turn red? Because it saw the salad dressing! Cheer up, laughter is the best salad dressing for the soul",style={'fontSize': '25px'})
        elif selected_mood == 'angry':
            return html.Div("Why was the math book sad? Because it had too many problems! Take a breather, let's turn those math problems into math puns", style={'fontSize': '25px'})
        elif selected_mood == 'tired':
            return html.Div("Why did the bicycle fall over? Because it was two-tired! Time for some well-deserved rest, you've earned it",style={'fontSize': '25px'})

# Callback to display mood-specific GIF
@app.callback(
    Output('mood-gif', 'children'),
    [Input('mood-boost-button', 'n_clicks')]
)
def display_image(n_clicks):
    image_urls = [
        'https://i0.wp.com/katzenworld.co.uk/wp-content/uploads/2019/06/funny-cat.jpeg?w=1920&ssl=1',
        'https://media.istockphoto.com/id/1427475185/photo/cut-british-shorthair-cat-with-slice-of-bread-on-the-head-in-a-living-room-at-vertical.webp?b=1&s=170667a&w=0&k=20&c=LVf-tJqGVNXJ6wL4uKgGtaULEmVCM0MbRsag23_0ys0=',
        'https://i.pinimg.com/736x/ba/92/7f/ba927ff34cd961ce2c184d47e8ead9f6.jpg',
    ]

    if n_clicks > 0:
        # Select a random image URL from the list
        image_url = random.choice(image_urls)

        return html.Div(
            html.Img(src=image_url, style={'width': '30%', 'height': '30%', 'borderRadius': '8px'}),
            style={'display': 'flex', 'justifyContent': 'center', 'alignItems': 'center', 'height': '100%'}
        )

if __name__ == '__main__':
    app.run_server(debug=True)

