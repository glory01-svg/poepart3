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


namespace RaceDayMVC.Models
{
    public class ErrorViewModel
    {
        public string? RequestId { get; set; }

        public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
    }
}
    namespace RaceDayMVC.Models
{
    public class EventCategoryViewModel
    {
       
            public int EventCategoryId { get; set; }
            public string CategoryName { get; set; }
            public decimal DistanceKm { get; set; }
            public decimal EntryFee { get; set; }
            public int Capacity { get; set; }
            public int EntriesCount { get; set; }

           
            public bool IsFull => EntriesCount >= Capacity;
        }
    }        
using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    
        public class EventViewModel
        {
            public int EventId { get; set; }

            [Required]
            [Display(Name = "Event Name")]
            public string EventName { get; set; }

            [Required]
            [Display(Name = "Date")]
            [DataType(DataType.Date)]
            public DateTime EventDate { get; set; }

            [Required]
            public string City { get; set; }

            [Required]
            public string Province { get; set; }

            public string Description { get; set; }

            [Display(Name = "Banner Image")]
            public string BannerImageUrl { get; set; }

            
            public List<EventCategoryViewModel> EventCategories { get; set; } = new List<EventCategoryViewModel>();
        }
    }
using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    public class LoginViewModel
    {
        
            [Required]
            [EmailAddress]
            public string Email { get; set; }

            [Required]
            [DataType(DataType.Password)]
            public string Password { get; set; }

            [Required]
            public string Role { get; set; } 
        }
    }
    
            
            public string PaymentStatus { get; set; }
            public string EntryStatus { get; set; }
        }
    }

