# cc_wk12_day3_pubsub

Draw out UML

Think about what views you will have based on the info given

  Index.HTML
  PubSub helper
  Views - SelectView
        - InstrumentInfoView
  Model - InstrumentFamilies
  
What should be in your HTML file?
  Title as this won't change
  Hardcode the options of the dropdown?
  Somewhere to hold the dynamic info - container?
  
Select list should be <option disable selected></option> as this will mean the dropdown starts without any option being selected.

PubSub.js is a helper object, the contents doesn't change
Publish is the dispatcher, Subscribe is the listener.

CustomEvent is an object, it would take in a string, we need to tell it what event it is listening for, i.e. channel

DispatchEvent is what sends out the info across the app that we have just created.  This method exists on the document, the browser gives us this.

  Step 1.
      Get data into the selectView
      Send data out into the app. Wite a method on the prototype of the model 'sendOutData'

      InstrumentFamilies.prototype.sendOutData = function () {
      PubSub.publish('instrumentFamilies: data-ready', this.instrumentFamilies);
      };
      (You need to require PubSub at the top of the page)

  Step 2.
      Create a new view in the views folder: select_view

      use the constructor to create a new selectView

      const SelectView = function () {
      }:

      write a method to receive the date:

      SelectView.prototype.receiveData = function() {
      PubScribe.subscribe('instrumentFamilies:data-ready', (event) => {
      const data = event.detail
      this.populateSelect(data)  - (this is written in step 3)
      };
      };
      (this is the same channel as above)
      Make sure you require PubSub at the top and export at the bottom.

      We now want to call these functions.

  Step 3.
      In app.js we will call the functions we require in InstrumentFamilies, selectView

      Document.addEventListener('DOMContentLoaded'() => {
      const SelectView():
      selectView.receiveData();
      (This happens first, so our listener is ready)
      **these are added later as they are written**
      selectView.setUpListenerForUserChoice() (this is written in step 5)

      const instrumentFamiliesModel = new InstrumentFamilies();
      instrumentFamiliesModel.sendOutData():

      We want to use the data to populate our selectView, receive data method
      We write a method populateSelect taking in the data, and used in the receiveData method in setp 2.

  Step 4.
      We want to follow the 3 steps
        1. Hook into the DOM using querySelector
        2. Create options and manipulate
        3. Append to the select

      SelectView.prototype.populateSelect = function (data) {
      const select = document.querySelector('select#instrument-families) - this is the ID in the html --This is step 1.
      data.forEach((family, index) => { 
      const option = document.createElement('option') -- This is step 2.
      option.textContent = family.name;
      option.value = index; (this is needed to pass the index back to the model)
      select.appendChild(option) -- this is step 3.
      };

  Step 5.
      We want to send our index of the selected option of the dropdown, so on the change of the selectView

      SelectView.prototype.setUpListenerForUserChoice = function (){
      const select = document.querySelector('select#instrument-families');
      select.addEventListener('change', (event) => {
      console.log(event.target.value) (this is later changed to:)
      const index = (event.target.value)
      PubSub.publish('SelectView:user-choice', index);
      };

  Step 6.
      In instrument_families we want to create a new method

      InstrumentFamilies.prototype.setUpListenerForUserChoice = function()
      PubSub subscribe('SelectView:user-choice', (event) => {
      console.log('index', event.detail) (this is later changed to: )
      const index = event.detail
      this.finObjectAndPublish(index) (this is written later in step 8)

  Step 7.
      Update the app.js
      Before we send out the data, call method
      instrumentFamiliesModel.setUpListenerForUserChoice

  Step 8.
      New method in instrument_families to send object to model
      InstrumentFamilies.prototype.findObjectAndPublish = function (index) {
      const familiyObject = this.intrumentFamilies[index];
      PubSub.publish('InstrumentFamilies:send-family', familyObject);

      We invoe this method when a change is made by the user (in step 6)

  Step 9.
      New view: instument_family_info
      Require PubSub
      constuctor
      const InstumentFamilyInfoView = function ()

      InstumentFamilyInfoView.prototype.setUpListenerForSelectedObject = function ()
      PubSub.subscribe('InstumentFamilies:send-family, (event) => {
      console.log('coming from view', event.detail) (this is later changed to:)
      const familyObject = event.detail (from step 10.)
      this.displayObject (from step 10.)

  Step 10. 
      Call this method in app.js
      New up a family view, require the InstumentFamilyInfoView 
      const instumentFamilyInfoView = new InstumentFamilyInfoView
      InstumentFamilyInfoView.setUpListenerForSelectedObject

      Create an empty div in your html
      InstumentFamilyInfoView.prototype.displayObject = function(object){
      const infoContainer = document.querySelector('#whateveryouhavenamedtheDiv')
      const paragraph = dcument.createElement('p')
      paragraph.textContent = object.name

      infoContainer.appendChild(paragraph)

End?
