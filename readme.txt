Car.cs

public interface ICar
    {
        int Id { get; set; }
        string Model { get; set; }
        float Price { get; set; }
        string Condition { get; set; }
        int Year { get; set; }
        ICar GetCar();
    }

///////////////////////////////////////////
Client

public class Client
    {
        private List<Industrial> industrials = new List<Industrial>();
        private List<Passenger> passengers = new List<Passenger>();
        
        public void AddIndustrial(int id, string model, float price, string condition, int year, string type, string brand)
        {
            industrials.Add(new Industrial(id, model, price, condition, year, type, brand));
        }
        
        public void AddPassenger(int id, string model, float price, string condition, int year, string type, string brand)
        {
            passengers.Add(new Passenger(id, model, price, condition, year, type, brand));
        }
        
        private List<ICar> GetIndustrials()
        {
            return industrials.Cast<ICar>().ToList();
        }
        
        private List<ICar> GetPassengers()
        {
            return passengers.Cast<ICar>().ToList();
        }

        private List<ICar> SearchByIndustry(string industry)
        {
            switch (industry)
            {
                case "Passenger":
                case "passenger":
                    return GetPassengers();
                case "Industrial":
                case "industrial":
                    return GetIndustrials();
                default:
                    return GetAllCars();
            }
        }

        private List<ICar> SearchByModel (string model)
        {
            List<ICar> result = new List<ICar>();
            foreach (var industrial in industrials)
            {
                if (industrial.GetModel().Contains(model))
                {
                    result.Add(industrial.GetCar());
                }
            }
            foreach (var passanger in passengers)
            {
                if (passanger.GetModel().Contains(model))
                {
                    result.Add(passanger.GetCar());
                }
            }
            return result;
        }

        public List<ICar> GetAllCars()
        {
            List<ICar> cars = new List<ICar>();
            foreach (var industrial in industrials)
            {
                cars.Add(industrial.GetCar());
            }
            foreach (var passenger in passengers)
            {
                cars.Add(passenger.GetCar());
            }
            return cars;
        }
        
        public List<ICar> Search(string model = "", string industry = "")
        {
            if (string.IsNullOrWhiteSpace(model) && !string.IsNullOrWhiteSpace(industry))
            {
                return SearchByIndustry(industry);
            }
            else if (!string.IsNullOrWhiteSpace(model) && string.IsNullOrWhiteSpace(industry))
            {
                return SearchByModel(model);
            }
            else if (!string.IsNullOrWhiteSpace(model) && !string.IsNullOrWhiteSpace(industry))
            {
                return SearchByModel(model).Intersect(SearchByIndustry(industry)).ToList();
            }
            else
            {
                return GetAllCars();
            }
//////////////////////////////////////////////////////////////////////////////////////////////////////
Command
public class Command
    {
        private Client Client = new Client();
        private string IndustryChoice { get; set; }
        private string ModelInput { get; set; }

        public Command(Client client)
        {
            this.Client = client;
        }
        
        private void SetIndustryChoice(string industryChoice)
        {
            this.IndustryChoice = industryChoice;
        }
        
        private void SetModelInput(string modelInput)
        {
            this.ModelInput = modelInput;
        }
        
        private void AskForModel()
        {
            Console.Write("Please enter the model of the car you want to buy: ");
            string input = Console.ReadLine();
            if (!string.IsNullOrWhiteSpace(input))
            {
                SetModelInput(input);
            }
            else
            {
                Console.WriteLine("You didn't enter anything, please try again");
                AskForModel();
            }
        }
        
        private void AskForIndustry()
        {
            Console.Write("Please enter the industry type of the car you want to buy: ");
            string input = Console.ReadLine();
            if (!string.IsNullOrWhiteSpace(input))
            {
                SetIndustryChoice(input);
            }
            else
            {
                Console.WriteLine("You didn't enter anything, please try again");
                AskForIndustry();
            }
        }
        
        private void ShowCars()
        {
            Console.WriteLine("Here are the cars we have in stock:");
            foreach (var car in Client.GetAllCars())
            {
                Console.WriteLine(car.ToString());
            }
        }
        
        private void ShowResult()
        {
            List<ICar> search = this.Client.Search(this.ModelInput, this.IndustryChoice);
            foreach (var result in search) 
            {
                Console.WriteLine(result.ToString());
            }
        }

        public void Start()
        {
            ShowCars();
            AskForIndustry();
            AskForModel();
            ShowResult();
        }
    }
/////////////////////////////////////
Industrial
 public class Industrial : ICar
    {
        public int Id { get; set; }
        public string Model { get; set; }
        public float Price { get; set; }
        public string Condition { get; set; }
        public int Year { get; set; }
        private string Type { get; } 
        private string Brand { get; }
        
        public Industrial(int id, string model, float price, string condition, int year, string type, string brand)
        {
            Id = id;
            Model = model;
            Price = price;
            Condition = condition;
            Year = year;
            Type = type;
            Brand = brand;
        }

        public ICar GetCar()
        {
            return this;
        }
        
        public string GetModel()
        {
            return Model;
        }
        
        public override string ToString()
        {
            return $"{Id.ToString()}\t{Model}\t{Price.ToString("C")}\t{Condition}\t{Year.ToString()}\t{Type}\t{Brand}";
        }
//////////////////////////////////////////////////
Passenger
 public class Passenger : ICar
    {
        public int Id { get; set; }
        public string Model { get; set; }
        public float Price { get; set; }
        public string Condition { get; set; }
        public int Year { get; set; }
        private string Type { get; } 
        private string Brand { get; }
        
        public Passenger(int id, string model, float price, string condition, int year, string type, string brand)
        {
            Id = id;
            Model = model;
            Price = price;
            Condition = condition;
            Year = year;
            Type = type;
            Brand = brand;
        }
        
        public ICar GetCar()
        {
            return this;
        }

        public string GetModel()
        {
            return Model;
        }
        
        public override string ToString()
        {
            return $"{Id.ToString()}\t{Model}\t{Price.ToString("C")}\t{Condition}\t{Year.ToString()}\t{Type}\t{Brand}";
        }
/////////////////////////////////////////////////
Program
class Program
    {
        public static void Main()
        {
            Client client = new Client();
            client.AddIndustrial(0, "Ford Focus RS", 4000, "Bad", 2015, "Sedan", "Ford");
            client.AddIndustrial(1, "Ford Mustang", 5000, "Good", 2015, "Sedan", "Ford");
            client.AddPassenger(2, "Tesla Roadster", 3000, "OK", 2020, "Sedan", "Tesla");

            Command cli = new Command(client);
            cli.Start();
        } 
    }
////////////////////////////////////////
