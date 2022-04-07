# The ReadMe file covers:
#### - Class Diagram 
#### - Given Usecases
#### - Design Justification
#### - How to run the code



# Class Diagram 
<p align="center">
<img width="595" alt="Screen Shot 2021-12-05 at 8 47 38 AM" src="https://user-images.githubusercontent.com/34672064/144755505-55a6b21f-f797-4b49-b997-009fe5e84c29.png">




# Given Usecases

 Assumption taken:
 If remaining threshold is 2 for a certain category and user has picked 3 of the items; he is charged for 2 and the remaining is mentioned in the erro_reciept file can be seen in input 2.
 
| Input | Input File | Reciept File | Reciept Error File | Intermediate Step |
|:---:|:---:|:---:|:---:|:---:|
|  1 | <img width="254" alt="Screen Shot 2021-12-04 at 9 39 17 PM" src="https://user-images.githubusercontent.com/34672064/144735157-403d43de-a85e-4637-99e7-c8bd283f42c7.png"> |<img width="334" alt="Screen Shot 2021-12-05 at 10 17 08 AM" src="https://user-images.githubusercontent.com/34672064/144758461-bc08ec82-1a8c-4993-9aae-03be460d8bef.png"> | Reciept Error : Empty file generated. | <img width="185" alt="Screen Shot 2021-12-05 at 9 05 08 AM" src="https://user-images.githubusercontent.com/34672064/144756138-11839286-7745-4c48-87c6-d6ed7df0662f.png"> |
|:---:|:---:|:---:|:---:|:---:|
|  2 | <img width="269" alt="Screen Shot 2021-12-04 at 9 40 28 PM" src="https://user-images.githubusercontent.com/34672064/144735189-b07d9524-f264-486c-8218-3c94a749069e.png"> | <img width="331" alt="Screen Shot 2021-12-05 at 10 18 00 AM" src="https://user-images.githubusercontent.com/34672064/144758480-505d9127-2e35-4f11-857f-e3efabfe606c.png"> | <img width="628" alt="Screen Shot 2021-12-04 at 9 42 20 PM" src="https://user-images.githubusercontent.com/34672064/144735231-a5c0cabf-4038-490a-a026-3099c3fd96ad.png"> |<img width="155" alt="Screen Shot 2021-12-05 at 9 02 59 AM" src="https://user-images.githubusercontent.com/34672064/144756059-38ab3b6d-c840-43db-9c98-fe0889cfffdd.png"> | <img width="148" alt="Screen Shot 2021-12-05 at 9 03 21 AM" src="https://user-images.githubusercontent.com/34672064/144756079-89988dd6-dd45-4a60-8baa-91465a02be60.png"> |
|:---:|:---:|:---:|:---:|:---:|
|  3 | <img width="301" alt="Screen Shot 2021-12-04 at 9 42 53 PM" src="https://user-images.githubusercontent.com/34672064/144735238-46708d6c-967e-4961-9e09-2f0b98ba7e92.png"> | <img width="338" alt="Screen Shot 2021-12-05 at 10 18 47 AM" src="https://user-images.githubusercontent.com/34672064/144758510-477a9d8a-6932-442a-bb3a-b4cbe9501158.png"> | Reciept Error : Empty file generated. | <img width="213" alt="Screen Shot 2021-12-05 at 9 00 51 AM" src="https://user-images.githubusercontent.com/34672064/144755944-20c030a6-2f7c-49d8-8450-09ee63abe13a.png"> |



# Design Justification
### Factory Design Pattern :
 Irrespective of which item from the category the user selects (luxury,essentials or miscellaneous) the behaviour can be abstracted and still have different business logics within it.For example, how threshold have been maintained differently in the given usecase.
 
 #### Implementation
 Additionally this pattern can be easily used to extend if a new category is added they will have to just implement the FactoryInterface and initialized once in the FactoryOpenerSingleton.
 ```
      public interface FactoryInterface {
         public void changeThreshold(int threshold);
         public void decrementThreshold(int counter);
         public int thresholdExists(int counter);
         public ItemPOJO addItem(String item, float price, Long quantity, int counter);
         public void setCart(CardNumberList cnl);
         public String checkPurchase(String checkItem,int checkQuant);
     }
 ```
 
 
### Singleton Design Pattern :
 At a given time only one instance of all the factories should be open if multiple instance of them are initialized the thresholds will not true any more hence this design pattern has been chosen.
 
#### Implementation
 In FactoryOpenerSingleton the essentialsFactory, luxuryFactory, miscellaneousFactory are initialized to null only on the first call instances are initialized and those instance are reused any time FactoryOpenerSingleton will be called.
 
```
     public FactoryOpenerSingleton getInstance(InverntoryCreatorAdapter ica) throws IOException {
        if(essentialsFactory==null) {
            essentialsFactory = new EssentialsFactory(ica,ica.reciept,ica.reciept_error);
        }
        if(luxuryFactory==null) {
            luxuryFactory = new LuxuryFactory(ica,ica.reciept,ica.reciept_error);
        }
        if(miscellaneousFactory==null) {
            miscellaneousFactory = new MiscellaneousFactory(ica,ica.reciept,ica.reciept_error);
        }
        return this;
    }
```
 
 
### Adapter Design Pattern :
On initialization of the inventory the json data is parsed and converted to into Item POJO (plain old JAVA objects) and this is mandatory as the factories will not have to parse the JSON every time to extract attributes.
 
#### Implementation
```
   public InverntoryCreatorAdapter() throws IOException {
          //JSON parser object to parse read file
          JSONParser jsonParser = new JSONParser();

          try (FileReader reader = new FileReader("inventory.json"))
          {
              Object obj = jsonParser.parse(reader);
              JSONArray itemList = (JSONArray) obj;
              System.out.println(itemList);

              //Iterate over item array
              itemList.forEach( item ->
              {parseInventoryObject( (JSONObject) item );});
 
          } catch (FileNotFoundException e) {
              e.printStackTrace();
          } catch (IOException e) {
              e.printStackTrace();
          } catch (org.json.simple.parser.ParseException e) {
              e.printStackTrace();
          }
      }
```



# How to run the code 
(1) Clone the repository.

(2) Run mvn-clean-install.

(3) The file of the order has to be passed through command line arguments. Edit the configurations in intelliJ , as shown to set the path of the order file :
<p align="center">
<img width="500" alt="Screen Shot 2021-12-04 at 9 50 56 PM" src="https://user-images.githubusercontent.com/34672064/144735493-2c06198f-2247-4d42-942e-22c1179299d2.png">

<img width="500" alt="Screen Shot 2021-12-04 at 9 51 12 PM" src="https://user-images.githubusercontent.com/34672064/144735491-ac6e7243-55a5-4413-ac67-47fefaf43195.png">
 </p>

(5) Run the project. The reciept.txt and recipt_error.txt will be  generated in the root folder itself , as shown :
<p align="center">
<img width="369" alt="Screen Shot 2021-12-04 at 9 56 49 PM" src="https://user-images.githubusercontent.com/34672064/144735539-3466f58b-211c-43e1-b5ac-4ee6db20f2ab.png">
 </p>
