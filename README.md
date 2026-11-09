# poepart3
namespace RaceDayMVC.Models
{
    public class EnrollmentViewModel
    {
       
            public int EntryId { get; set; }
            public int RaceNumber { get; set; }

            
            public string EventName { get; set; }
            public DateTime EventDate { get; set; }

            
            public string CategoryName { get; set; }

            
            public string UserFullName { get; set; }
            public string UserEmail { get; set; }

            

            
            public string PaymentStatus { get; set; }
            public string EntryStatus { get; set; }
        }
    }

