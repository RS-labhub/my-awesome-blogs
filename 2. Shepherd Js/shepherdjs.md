## Shepherd: Guide your users with a new JavaScript library

![cover image](<shepherd v1.gif>)

"_A person who protects, guides, or watches over a sheep or flocks of sheep is called a Shepherd_". But how devs can do shepherding?? **Shepherd Js**, one of the best OS tools to guide your users, and enable them to understand your application or a specific part of your application in a detailed and descriptive way.
&nbsp;
## What is [Shepherd](https://github.com/shepherd-pro/shepherd)?? 🐏
Shepherd is a JavaScript library for guiding users through your app. It uses Floating UI, another open-source library, to render dialogs for each tour "step".
> In short, guide your users through a tour of your app.

You can create custom user onboarding tours, training, and announcements to drive user adoption.

## How to do Shepherding? 🕺🐑
Shepherd enables you to guide users through a custom tour or journey within your app or website. Highly customizable with minimal styles, Shepherd allows for powerful customization while being easy to use. Various frameworks are supported including React, Ember, Angular, Vue.js, ES Modules, or plain JavaScript.

### Steps to install Shepherd 🛠️:
1. You can install shepherd directly using npm, yarn, pnpm, and bun by using the below command:
 - npm install: `npm install shepherd.js`
 - yarn install: `yarn add shepherd.js`
 - pnpm install: `pnpm add shepherd.js`
 - bun install: `bun add shepherd.js`

2. You can directly use the jsDelivr cdn too, to use shepherd.
```json
<script
  type="module"
  src="https://cdn.jsdelivr.net/npm/shepherd.js@13.0.0/dist/esm/shepherd.mjs"
></script>
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/shepherd.js@13.0.0/dist/css/shepherd.css"
/>
```
&nbsp;
### How to create steps? 🪜
Creating shepherd tour are super easy, just follow the below code:
```js
const tour = new Shepherd.Tour({
		defaultStepOptions: {
			cancelIcon: {
				enabled: true
			},
			classes: 'class-1 class-2',
			scrollTo: { behavior: 'smooth', block: 'center' }
		}
	});

	tour.addStep({
		title: 'Creating a Shepherd Tour',
		text: 'Creating a Shepherd tour is easy.
               Just create a "Tour" instance, and add as many
               steps as you want.',
		attachTo: {
			element: '.hero-example',
			on: 'bottom'
		},
		buttons: [
			{
				action() {
					return this.back();
				},
				classes: 'shepherd-button-secondary',
				text: 'Back'
			},
			{
				action() {
					return this.next();
				},
				text: 'Next'
			}
		],
		id: 'creating'
	});

	tour.start(); //starts the tour
```
&nbsp;
### How to change styling? 💅
The HTML that the ShepherdJS library injects into the page looks something like this:
```html
<div aria-describedby="step1-description" role="dialog" tabindex="0" class="shepherd-element shepherd-enabled" data-shepherd-step-id="step1" style="position: absolute; inset: 0px auto auto 0px; margin: 0px; transform: translate(393px, 302px);" data-popper-placement="bottom">
   <div class="shepherd-arrow" data-popper-arrow="" style="position: absolute; left: 0px; transform: translate(192px);"></div>
   <div class="shepherd-content">
      <div class="shepherd-text" id="step1-description">Users seem to be down by 12.4%. Is that bad?</div>
      <footer class="shepherd-footer"><button class=" shepherd-button " tabindex="0">Next</button></footer>
   </div>
</div>
```
Therefore, making it easier to target elements (for example the arrow) with something like the below..
```html
.shepherd-arrow {
  /* Some styles here... */
}
```
Also, if you noticed, when you added the steps, `id` is used. Therefore, that can also be used to target the description of a particular step.
&nbsp;
The above ones are some basic properties. If someone wants to show the **progress of the tour**, they can do that too! Follow the below lines of code:
```js
when: {
  show() {
    const currentStep = Shepherd.activeTour?.getCurrentStep();
    const currentStepElement = currentStep?.getElement();
    const header = currentStepElement?.querySelector('.shepherd-header');
    const progress = document.createElement('span');
    progress.style['margin-right'] = '315px';
    progress.innerText = `${Shepherd.activeTour?.steps.indexOf(currentStep) + 1}/${Shepherd.activeTour?.steps.length}`;
    header?.insertBefore(progress, currentStepElement.querySelector('.shepherd-cancel-icon'));
  }
}
```
&nbsp;
You can get all the details in the **Shepherd's Official Doc Page**, _link provided here_: https://docs.shepherdpro.com/
&nbsp;

## Cool Projects using Shepherd Js 🧑‍💻
### 1️⃣ Tic-Tac-Toe ⭕❌

Either attending a boring lecture, or having a serious conversation with your best-friend, or in any moment of life, we have played this game, tic-tac-toe.
An awesome project to support and re-alive this game has been made by **Susheel Thapa**. The project is OS. This project shows one of the best uses of Shepherd.

_Live Demo_: https://tictactoe.susheelthapa.com.np/
_Github Link_: https://github.com/SusheelThapa/Tic-Tac-Toe
_Shepherd Implementation_: https://github.com/SusheelThapa/Tic-Tac-Toe/tree/main/src/components/Shepherd

![tic-tac-toe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/aztwpbdw5f1s9v5ecqtj.png)
<figcaption> Guided tour of the game using Shepherd Js </figcaption>
&nbsp;

### 2️⃣ Studymate 📖

StudyMate is an innovative web application designed to enhance the learning experience by providing pagewise summaries of PDFs and generating interactive flashcards. With the integration of Shepherd.js, StudyMate offers a guided tour feature to help users navigate through the application's functionalities seamlessly. StudyMate makes it particularly useful for last-minute revision sessions, allowing users to quickly review key concepts and information in a structured manner.

This project is built by **Subash Lamichhane**.

_Github Link_: https://github.com/Subash-Lamichhane/StudyMate
_Shepherd Implementation_: https://github.com/Subash-Lamichhane/StudyMate/blob/main/frontend/src/pages/LandingPage.jsx

![study-mate](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/is8kshz1dd33f8x5ck7s.png)
<figcaption> Guided tour of Study Mate with animation using Shepherd Js </figcaption>
&nbsp;

### 3️⃣ SeedLink 🌱
SeedLink is a frontend web application that lets you explore a diverse range of plants available at nearby nurseries and gardens 🌿. You can buy it, or research about it. That's it?? Naah, explore the app to learn more! 🪴

This project is built by **Rohan Sharma** (yes, I'm that creator)

_Github Link_: https://github.com/RS-labhub/SeedLink-Shepherd
_Shepherd Implementation_:  https://github.com/RS-labhub/SeedLink-Shepherd/blob/main/src/shepherd.js


![seed-link](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9vor6p22i2mia0fvhz5j.png)
<figcaption> Guided tour of SeedLink with visualizing and appealing theme using Shepherd Js </figcaption>
&nbsp;

### Moving to end... 🥹
Shepherd is an open-source, fully featured, digital adoption platform (DAP) and user onboarding service. 

Shepherd has also evolved into the pro version which is a _paid service_. Here is the list of **features provided by Shepherd-Pro**:
- Customizable Tour Templates
- Analytics Integration
- Multi-Language Support
- User Behavior Tracking
- Integration Capabilities
- Responsive Design
- Feedback Mechanisms
- Advanced Branching Logic

If you still haven't used this, at least visit it once! I promise, you won't regret 🥰

{% cta https://github.com/shepherd-pro/shepherd %} Star Shepherd on Github ⭐ {% endcta %}
